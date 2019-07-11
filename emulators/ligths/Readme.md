# light controller web service

## Local deploy

1. Create virtualenv ```virtualenv controllerEnv```
2. Activate virtualenv ```source <controllerEnv path>/bin/activate```
2. Install requirements ```pip install -r requirements.txt```


3. Launch app ```./run_dev.sh```, by default it will run on ```localhost:8000/dashboard/<room_name>```

## Environment variables

To specify host Ip and Port you must set them as environment variables like:
```bash

export APP_PORT=9000
export APP_HOST=0.0.0.0
export APP_DEBUG=True

python src/server.py

```



## Controller switch
To switch between web application lights simulator and real pi controller you must set an environment variable ```export light_CONTROLLER=simulator``` or ```export light_CONTROLLER=controller```

## Command api
We process two REST endpoints with POST method data:

- ```setcolor``` illuminates one light based on id for an specific session. Json schema: 
```json

{"required": ["id", "color", "session"],
    "properties": {
        "id": { "type": "string" },
        "session": {"type": "string"},
        "color": { "type": "array", "items": { "type": "number", "minimum": 0,
  "maximum": 255} },
    }

```

- ```setbulk``` illuminates lights collection based on id for an specific session. Json schema: 

```json

{
    "required": ["set", "session"],
    "properties": {
        "session": {
            "type": "string"
        },
        "set": {
            "type": "array",
            "items": {
                "type": "object",
                "properties": {
                    "id": {
                        "type": "string"
                    },
                    "color": {
                        "type": "array",
                        "items": {
                            "type": "number",
                            "minimum": 0,
                            "maximum": 255
                        }
                    }
                }
            }
        }
    }
}

```

 
## Access simulator view
Simulator app works recieving and processing sent API commands through a websocket channel. Each simulator web app runs in an specific session_name, for example, ```http://localhost:8000/dashboard/test``` will process and draw all sent messages with ```test``` as session id.

### Pipeline

1. Send command to ```http://localhost:8000/setcolor``` with data ```{"id": "1", "color": [100, 200, 50], "session": "test"}```
2. In all browser tabs (opened in ```http://localhost:8000/dashboard/test```) you will see command logs and drawing simulation