---
layout: post
title: swift小项目-基于CoreLoaction和MapKit的位置app
date: 2016-03-23
categories: blog
tags: [swift]
description: 基于CoreLoaction和MapKit的位置app

---

这是一个用了CoreLoaction和MapKit的一个小项目，界面是点击按钮找到当前位置并且在地图上标记出来。下面是几点注意的：

1.使用CoreLocation时，requestAlwaysAuthorization(),因为要获取位置权限，所以在info.plist里要添加NSLocationAlwaysUsageDescription和NSLocationWhenInUseUsageDescription这两个授权提示的描述，不然在debug的时候是进不到delegate实现的方法的。



2.下面直接贴出部分代码

    import UIKit
    import CoreLocation
    import MapKit

    class ViewController: UIViewController , CLLocationManagerDelegate{

        @IBOutlet weak var locationLabel: UILabel!
        @IBOutlet weak var mapView: MKMapView!
        
        var locationManager: CLLocationManager!
        
        override func viewDidLoad() {
            super.viewDidLoad()
            self.mapView.mapType = MKMapType.Standard
        }

        override func didReceiveMemoryWarning() {
            super.didReceiveMemoryWarning()
        }

        @IBAction func FindButtonDidPressed(sender: AnyObject) {
            
            locationManager = CLLocationManager()
            locationManager.delegate = self
            locationManager.desiredAccuracy = kCLLocationAccuracyBest
            locationManager.requestAlwaysAuthorization()
            locationManager.startUpdatingLocation()
            
            //创建一个MKCoordinateSpan对象，设置地图的范围（越小越精确）
            let latDelta = 0.05
            let longDelta = 0.05
            let currentLocationSpan:MKCoordinateSpan = MKCoordinateSpanMake(latDelta, longDelta)
            
            //使用当前位置
            var center:CLLocation = locationManager.location!
    //        //使用自定义位置
    //        let center:CLLocation = CLLocation(latitude: 32.029171, longitude: 118.788231)
            let currentRegion:MKCoordinateRegion = MKCoordinateRegion(center: center.coordinate,
                                                                      span: currentLocationSpan)
            
            //设置显示区域
            self.mapView.setRegion(currentRegion, animated: true)
            
            //创建一个大头针对象
            let objectAnnotation = MKPointAnnotation()
            objectAnnotation.coordinate = center.coordinate
            objectAnnotation.title = center.description
            objectAnnotation.subtitle = ""
            self.mapView.addAnnotation(objectAnnotation)
            
        }
        
        func locationManager(manager: CLLocationManager, didFailWithError error: NSError) {
        
            self.locationLabel.text = "更新位置发生错误：" + error.description
        }
        
        func locationManager(manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
            
            CLGeocoder().reverseGeocodeLocation(manager.location!, completionHandler: {(placemarks, error)->Void in
                
                if (error != nil) {
                    self.locationLabel.text = "Reverse geocoder failed with error" + error!.localizedDescription
                    return
                }
                
                if placemarks!.count > 0 {
                    let pm = placemarks![0]
                    self.displayLocationInfo(pm)
                } else {
                    self.locationLabel.text = "Problem with the data received from geocoder"
                }
            })
        }
        
        func displayLocationInfo(placemark:CLPlacemark?){
            
            if let containsPlacemark = placemark {
                
                locationManager.stopUpdatingLocation()
                
                let locality = (containsPlacemark.locality != nil) ? containsPlacemark.locality : ""
                let postalCode = (containsPlacemark.postalCode != nil) ? containsPlacemark.postalCode : ""
                let administrativeArea = (containsPlacemark.administrativeArea != nil) ? containsPlacemark.administrativeArea : ""
                let country = (containsPlacemark.country != nil) ? containsPlacemark.country : ""
                
                self.locationLabel.text = locality! + "-" +  postalCode! + "-" +  administrativeArea! + "-" +  country!
            }
        }
    }
