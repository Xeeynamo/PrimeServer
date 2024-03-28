# PrimeServer

Fork of the original PrimeServer with a number of improvements.

## Changelog

* Passing or failing any song will update the existing modifiers, note skins and rush mode settings.
* Ending a game session will update the existing modifiers, note skins and rush mode settings.
* Passing or failing any song will store the highest score in the "MY BEST" for any future sessions.

## Build

The command below will statically link any dependency (e.g. libc) in the final executable.
This is very important whenever the server is deployed on an older Linux version than the one the server is built onto.

```bash
CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo .
```

## REST endpoints

The server exposes a few API endpoints to perform some basic operations.

### Create profile

The `Name` field cannot exceed 12 characters. The returned payload will contain an access-code, a 32 characters long
hexadecimal string that will be used as an authentication token for your new personal profile.
Additionally a file called `prime.bin` will be generated. That file needs to be stored at the root of a FAT32 formatted
USB drive for PIU Prime to being able to send the authentication token to the server.

```bash
server_ip=127.0.0.1
json_payload=$(cat <<EOF
{
  "Name": "YOUR_NAME",
  "Avatar": 0,
  "CountryID": 0,
  "Modifiers": 0,
  "SpeedMod": 0
}
EOF
)

response=$(curl -X POST -H "Content-Type: application/json" -d "$json_payload" http://$server_ip:8090/createProfile)
echo $response
echo -n "$(echo "$response" | grep -o '"AccessCode":"[^"]*' | cut -d'"' -f4)" | xxd -r -p > prime.bin
```