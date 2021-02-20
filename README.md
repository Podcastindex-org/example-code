# example-code
Code examples for the API.

For full example repositories, go here:  https://github.com/Podcastindex-org-Examples

**PHP**

* * *


    //Required values  
    $apiKey = "UXKCGDSYGUUEVQJSYDZH";  
    $apiSecret = "yzJe2eE7XV-3eY576dyRZ6wXyAbndh6LUrCZ8KN|";  
    $apiHeaderTime = time();  

    //Hash them to get the Authorization token  
    $hash = sha1($apiKey.$apiSecret.$apiHeaderTime);  

    //Set the required headers  
    $headers = [  
        "User-Agent: SuperPodcastPlayer/1.3",  
        "X-Auth-Key: $apiKey",  
        "X-Auth-Date: $apiHeaderTime",  
        "Authorization: $hash"  
    ];  

    //Make the request to an API endpoint  
    $ch = curl_init();  
    curl_setopt($ch, CURLOPT_URL,"https://api.podcastindex.org/api/1.0/search/byterm?q=bastiat");  
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);  
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);  

    //Collect and show the results  
    $response = curl_exec ($ch);  
    curl_close ($ch);  
    echo print_r(json_decode($response), TRUE);  



**C#**

* * *


    //Required values  
    string apiKey = "UXKCGDSYGUUEVQJSYDZH";  
    string apiSecret = "yzJe2eE7XV-3eY576dyRZ6wXyAbndh6LUrCZ8KN|";  
    TimeSpan t = DateTime.UtcNow - new DateTime(1970, 1, 1);  
    int apiHeaderTime = (int)t.TotalSeconds;  

    //Hash them to get the Authorization token  
    string hash = "";  
    using (SHA1Managed sha1 = new SHA1Managed())  
    {  
        var hashed = sha1.ComputeHash(Encoding.UTF8.GetBytes(apiKey + apiSecret + apiHeaderTime));  
        var sb = new StringBuilder(hashed.Length * 2);  

        foreach (byte b in hashed)  
        {  
            // can be "x2" if you want lowercase  
            sb.Append(b.ToString("x2"));  
        }  

        hash = sb.ToString();  
    }  

    //Create the web request and add the required headers  
    HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://api.podcastindex.org/api/1.0/search/byterm?q=bastiat");  
    request.Headers.Add("User-Agent", "SuperPodcastPlayer/1.3");  
    request.Headers.Add("X-Auth-Date", apiHeaderTime.ToString());  
    request.Headers.Add("X-Auth-Key", apiKey);  
    request.Headers.Add("Authorization", hash);  

    //Send the request and collect/show the results  
    try  
    {  
        WebResponse webResponse2 = request.GetResponse();  
        Stream stream2 = webResponse2.GetResponseStream();  
        StreamReader reader2 = new StreamReader(stream2);  

        Console.WriteLine(reader2.ReadToEnd());  

        webResponse2.Close();  
    }  
    catch (Exception e)  
    {  
        Console.WriteLine("Error.");  
    }  

**Swift**

* * *

    import Foundation
    import CommonCrypto

    extension String {
        func sha1() -> String {
            let data = Data(self.utf8)
            var digest = [UInt8](repeating: 0, count:Int(CC_SHA1_DIGEST_LENGTH))
            data.withUnsafeBytes {
                _ = CC_SHA1($0.baseAddress, CC_LONG(data.count), &digest)
            }
            let hexBytes = digest.map { String(format: "%02hhx", $0) }
            return hexBytes.joined()
        }
    }

    let apiKey = "UXKCGDSYGUUEVQJSYDZH"
    let apiSecret = "yzJe2eE7XV-3eY576dyRZ6wXyAbndh6LUrCZ8KN|"
    let apiHeaderTime = String(Int(Date().timeIntervalSince1970))
    let hash = (apiKey + apiSecret + apiHeaderTime).sha1()

    var semaphore = DispatchSemaphore (value: 0)
    var request = URLRequest(url: URL(string: "https://api.podcastindex.org/api/1.0/search/byterm?q=bastiat")!,timeoutInterval: Double.infinity)
    request.addValue("SuperPodcastPlayer/1.3", forHTTPHeaderField: "User-Agent")
    request.addValue(apiKey, forHTTPHeaderField: "X-Auth-Key")
    request.addValue(apiHeaderTime, forHTTPHeaderField: "X-Auth-Date")
    request.addValue(hash, forHTTPHeaderField: "Authorization")

    request.httpMethod = "GET"

    let task = URLSession.shared.dataTask(with: request) { data, response, error in
        guard let data = data else {
            print(String(describing: error))
            return
        }
        print(String(data: data, encoding: .utf8)!)
        semaphore.signal()
    }

    task.resume()
    semaphore.wait()

**Node**

Example uses [node-fetch](https://www.npmjs.com/package/node-fetch)

* * *
    
    const fetch = require('node-fetch')
    const crypto = require('crypto')

    
    //Required values  
    const APIKEY = 'UXKCGDSYGUUEVQJSYDZH'
    const APISECRET = 'yzJe2eE7XV-3eY576dyRZ6wXyAbndh6LUrCZ8KN|'

    const ts = Math.floor(Date.now() / 1000)
    const authString = APIKEY + APISECRET + ts.toString()
    const authHeader = crypto.createHash('sha1').update(authString).digest('hex')

    fetch('https://api.podcastindex.org/api/1.0/search/byterm?q=bastiat',
      {
        method: 'GET',
        headers: {
          "User-Agent" : "homemade pod-fetcher 0.026b",
          "X-Auth-Date" : ts,
          "X-Auth-Key"  : APIKEY,
          "Authorization" : authHeader
        },

      }
    ).then(
      res => {
        console.log(res.status, res.statusText)
        console.log(res.headers)
        console.log(res)
        return res.json()
      }
    ).then(
      data => {
        console.log('got data:')
        console.log(data)
      },
      err => {
        console.log('got error:')
        console.log(err)
      },
    )



