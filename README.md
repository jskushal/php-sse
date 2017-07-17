# SSE: Server Sent Event

## Run demo

- Run PHP webserver
```Bash
php -S 127.0.0.1:9001 -t .
```

- Open url `http://127.0.0.1:9001/push.html`

![Demo](https://raw.githubusercontent.com/hhxsv5/SSE/master/sse.png)

## Javascript demo

```Javascript
//withCredentials=true: pass the cross-domain cookies to server-side
var source = new EventSource("http://127.0.0.1:9001/push.php", {withCredentials:true});
source.addEventListener('new-msg', function(event){
    console.log(event.data);//get data
}, false);
```

## Laravel demo

```PHP
//Action method in the controller
public function newMsgs()
{
    $response = new \Symfony\Component\HttpFoundation\StreamedResponse();
    $response->headers->set('Content-Type', 'text/event-stream');
    $response->headers->set('Cache-Control', 'no-cache');
    $response->headers->set('Connection', 'keep-alive');
    $response->headers->set('X-Accel-Buffering', 'no');//Nginx: unbuffered responses suitable for Comet and HTTP streaming applications
    $response->setCallback(function () {
        (new SSE())->start(new Update(function () {
            $id = mt_rand(1, 1000);
            $newMsgs = [['id' => $id, 'title' => 'title' . $id, 'content' => 'content' . $id]];//get data from database or servcie.
            if (!empty($newMsgs)) {
                return json_encode(['newMsgs' => $newMsgs]);
            }
            return false;//return false if no new messages
        }), 'new-msgs');
    });
    return $response;
}
```
