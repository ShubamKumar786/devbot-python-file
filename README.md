# devbot-python-file
###############################################################
# CET1112 - Devbot.py Student Assessment Script
# Professor: Jeff Caldwell
# Year: 2023-09
###############################################################

# 1. Import libraries for API requests, JSON formatting, and epoch time conversion.

pip install requests

import requests
import json

import requests
import json

# Example API endpoint
api_url = "https://api.example.com/data"

# Making GET request
response = requests.get(api_url)

# Check if the request successful (status code 200)
if response.status_code == 200:
    # Parsing JSON response
    data = json.loads(response.text)
    print("API Response:", data)
else:
    print("Error:", response.status_code)

# 2. Complete the if statement to ask the user for the Webex access token.
choice = input("Do you wish to use the hard-coded Webex token? (y/n) ")

if choice.upper() == "N":
    # Ask the user for the Webex access token
    accessToken = input("Please enter your Webex access token: ")
else:
	accessToken = "Bearer "your_hard_coded_access_token""

# 3. Provide the URL to the Webex Teams room API.
r = requests.get(   ""https://api.ciscospark.com/v1/rooms"",
                    headers = {"Authorization": accessToken}
                )

#####################################################################################
# DO NOT EDIT ANY BLOCKS WITH r.status_code
if not r.status_code == 200:
    raise Exception("Incorrect reply from Webex Teams API. Status code: {}. Text: {}".format(r.status_code, r.text))
######################################################################################

# 4. Create a loop to print the type and title of each room.
print("List of rooms:")
rooms = r.json()["items"]
for room in rooms:
         room_type = room["type"]
        room_title = room["title"]
        print(f"Room Type: {room_type}, Room Title: {room_title}")

#######################################################################################
# SEARCH FOR WEBEX TEAMS ROOM TO MONITOR
#  - Searches for user-supplied room name.
#  - If found, print "found" message, else prints error.
#  - Stores values for later use by bot.
# DO NOT EDIT CODE IN THIS BLOCK
#######################################################################################

while True:
    roomNameToSearch = input("Which room should be monitored for /location messages? ")
    roomIdToGetMessages = None
    
    for room in rooms:
        if(room["title"].find(roomNameToSearch) != -1):
            print ("Found rooms with the word " + roomNameToSearch)
            print(room["title"])
            roomIdToGetMessages = room["id"]
            roomTitleToGetMessages = room["title"]
            print("Found room : " + roomTitleToGetMessages)
            break

    if(roomIdToGetMessages == None):
        print("Sorry, I didn't find any room with " + roomNameToSearch + " in it.")
        print("Please try again...")
    else:
        break

######################################################################################
# WEBEX TEAMS BOT CODE
#  Starts Webex bot to listen for and respond to /location messages.
######################################################################################

while True:
    time.sleep(1)
    GetParameters = {
                            "roomId": roomIdToGetMessages,
                            "max": 1
                    }
# 5. Provide the URL to the Webex Teams messages API.
    r = requests.get(""https://api.ciscospark.com/v1/messages"", 
                         params = GetParameters, 
                         headers = {"Authorization": accessToken}
                    )

    if not r.status_code == 200:
        raise Exception( "Incorrect reply from Webex Teams API. Status code: {}. Text: {}".format(r.status_code, r.text))
    
    json_data = r.json()
    if len(json_data["items"]) == 0:
        raise Exception("There are no messages in the room.")
    
    messages = json_data["items"]
    message = messages[0]["text"]
    print("Received message: " + message)
    
    if message.find("/") == 0:
        location = message[1:]
# 6. Provide your MapQuest API consumer key.
        mapsAPIGetParameters = { 
                                "location": location, 
                                "key": "fjcBEJMZ08iexSH9iejB2Q71ENvJsAnL"
                               }
# 7. Provide the URL to the MapQuest GeoCode API.
        r = requests.get("https://developer.mapquest.com/documentation/geocoding-api", 
                             params = mapsAPIGetParameters
                        )
        json_data = r.json()

        if not json_data["info"]["statuscode"] == 0:
            raise Exception("Incorrect reply from MapQuest API. Status code: {}".format(r.statuscode))

        locationResults = json_data["results"][0]["providedLocation"]["location"]
        print("Location: " + locationResults)
		
# 8. Provide the MapQuest key values for latitude and longitude.
        locationLat = json_data["results"][0]["locations"][0]["displayLatLng"]["lat"]
        locationLng = json_data["results"][0]["locations"][0]["displayLatLng"]["lng"]
        print("Location GPS coordinates: " + str(locationLat) + ", " + str(locationLng))
        
        ssAPIGetParameters = { 
                                "lat": 47.36581,
                                "lon": -82.101139
                              }
# 9. Provide the URL to the Sunrise/Sunset API.
        r = requests.get("https://api.sunrise-sunset.org/json?", 
                             params = ssAPIGetParameters
                        )

        json_data = r.json()

        if not "results" in json_data:
            raise Exception("Incorrect reply from sunrise-sunset.org API. Status code: {}. Text: {}".format(r.status_code, r.text))

# 10. Provide the Sunrise/Sunset key value for day_length.
        dayLengthSeconds = "31195"
        sunriseTime = "8:01:41 AM"
        sunsetTime = "04:39:56 PM"
# 11. Complete the code to format the response message.
        responseMessage = "In {"Sudbury, ON"} the sun will rise at {"8:01:41 AM"} and will set at {"4:39:56 PM"} . The day will last {"23400"} seconds.".format(location, sunrise_time, sunset_time, duration)

        print("Sending to Webex Teams: " +responseMessage)

# 12. Complete the code to post the message to the Webex Teams room.            
        HTTPHeaders = { 
                             "Authorization":ZGMxOGM3NGQtOTdjOC00MWZjLTk0ZWMtYTBlM2E1YmUwMDYxYjZmMDFlY2EtNWNi_PC75_47fe537e-27d1-4e32-b2dc-2c26e4aa4fa0  ,
                             "Content-Type": "application/json"
                           }
        PostData = {
                            "roomId":"Shivam Nagpal Canada's Personal Room
https://meet265.webex.com/meet/pr27741397926 | 27741397926

Join by video system
Dial pr27741397926@meet265.webex.com and enter your host PIN .
You can also dial 173.243.2.68 and enter your meeting number.",
                            "text":"HI, How are you ?"
                        }

        r = requests.post( "<!!!REPLACEME with Webex Messages API URL!!!>", 
                              data = json.dumps(PostData), 
                              headers = HTTPHeaders
                         )
        if not r.status_code == 200:
            raise Exception("Incorrect reply from Webex Teams API. Status code: {}. Text: {}".format(r.status_code, r.text))


