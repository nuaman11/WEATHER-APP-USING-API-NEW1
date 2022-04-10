# WEATHER-APP-USING-API-NEW1
USING DATA PASS ; SEGUE



MAIN VIEWCONTROLLER

import UIKit
import CoreLocation
import Alamofire
import SwiftyJSON

class WeatherViewController: UIViewController, CLLocationManagerDelegate {

// Constants
    let WEATHER_URL = "https://api.openweathermap.org/data/2.5/weather"
    let APP_ID = "c9b1ae2f2a6845f0d3d7545b65b40156"
    
// TODO: Declare instances variable gere
    
    let locationManager = CLLocationManager()
    let weatherDataModel = WeatherDataModel()
    
    @IBOutlet weak var lblDegree: UILabel!
    @IBOutlet weak var img: UIImageView!
    @IBOutlet weak var lbl2: UILabel!
    
    var receivedata = ""
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        locationManager.delegate = self
        locationManager.desiredAccuracy = kCLLocationAccuracyHundredMeters
        locationManager.requestAlwaysAuthorization()
        locationManager.startUpdatingLocation()
        
        lbl2.text = receivedata
    }
    
    
    // write the didUpdate Locations method here
    
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        let location = locations[locations.count-1]
        if location.horizontalAccuracy > 0 {
            locationManager.stopUpdatingLocation()
            print("longitude = \(location.coordinate.longitude), latitute = \(location.coordinate.latitude)")
            
           // let latitude = String(location.coordinate.latitude)
           // let longitude = String(location.coordinate.longitude)
            
            let params : [String : String] = ["q" : receivedata , "appid" : APP_ID]
            
            getWeatherData(url : WEATHER_URL, parameters : params)
        }
    }
    
    
    // write the didFailWithError method here
    
    func locationManager(_ manager: CLLocationManager, didFailWithError error: Error) {
        print(error)
        lbl2.text = "Location Unavailable"
        
    }
    
    
   func getWeatherData(url : String, parameters : [String : String]) {
     
       Alamofire.request(url, method: .get, parameters: parameters).responseJSON {
           response in
           if response.result.isSuccess {
               print("Sucess! Got the weather data")
               let weatherJSON : JSON = JSON(response.result.value!)
               print(weatherJSON)
               self.updateWeatherData(json : weatherJSON )
           } else {
               print("Error \(response.result.error)")
               self.lbl2.text = "Connection Issue"
           }
       }
    }
    
    func updateWeatherData(json :JSON){
        
        if let tempResult = json["main"]["temp"].double {
        weatherDataModel.temperature = Int(tempResult - 273.15)
        weatherDataModel.city = json["name"].stringValue
        weatherDataModel.condition = json["weather"]["id"].intValue
        weatherDataModel.weatherIconName = weatherDataModel.updateWeatherIcon(condition: weatherDataModel.condition)
            updateUIWithWeatherData()
            
        } else {
            lbl2.text = "Weather Unavailable"
        }
    }
    
    func updateUIWithWeatherData(){
        lbl2.text = weatherDataModel.city
        lblDegree.text = "\(weatherDataModel.temperature)°"
        img.image = UIImage(named: weatherDataModel.weatherIconName)
    }
    
}





CREATE NEW COCOTOUCH CLASS FILE (WeatherDataModel)

import UIKit

class WeatherDataModel: UIViewController {

    var temperature : Int = 0
    var condition : Int = 0
    var city : String = ""
    var weatherIconName : String = ""
    
    
    func updateWeatherIcon(condition: Int) -> String{
        switch (condition) {
            
        case 0...300 :
            return "tstorm1"
            
        case 301...500 :
            return "light_rain"
            
        case 501...600 :
            return "shower3"
            
        case 601...700 :
            return "snow4"
        
        case 701...771 :
            return "fog"
            
        case 772...799 :
            return "tstorm3"
            
        case 800 :
            return "sunny"
            
        case 801...804 :
            return "cloudy2"
            
        case 900...903, 905...1000 :
            return "tstorm3"
            
        case 903 :
            return "snow5"
            
        case 904 :
            return "sunny"
            
        default :
            return "dunno"
        }
    
    }
}





create new viewcontroller as SecondViewController
import UIKit

class SecondViewController: UIViewController {

    @IBOutlet weak var txtBox: UITextField!

    
    
    override func viewDidLoad() {
        super.viewDidLoad()

       
    }
    
    @IBAction func btnsubmit(_ sender: UIButton) {
        
        
        performSegue(withIdentifier: "new1", sender: .none)
    }
    
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        let newobj = segue.destination as! WeatherViewController
        newobj.receivedata = txtBox.text!
    }

}
c

