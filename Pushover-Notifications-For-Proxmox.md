Method: `POST`
URL: `https://api.pushover.net/1/messages.json`
Headers: `Key: Content-Type Value: application/json`
Body:

```
{
  "token": "{{ secrets.apikey }}",
  "user": "{{ secrets.userkey }}",
  "title": "{{ title }}",
  "message": "{{ escape message }}",
  "priority": "0",
  "timestamp": "{{ timestamp }}"
}
```

Secrets:
Key: `userkey` Value: <your pushover user key>
Key: `apikey` Value: <your pushover application key>

Some options:

Priority​
For normal priority messages use `"priority": "0",`
For high priority messages use `"priority": "1",`

Emergency Messages
Use `"priority": "2",` and add the following (refer to Pushover documentation for what these settings do):
JSON:

```
"retry": "30",
"expire": "300",
"tags": "service=pve-emergency",
```

I haven't quite got emergency to work. The first message is sent out, but then subsequent emergency messages are not received. I suspect the Pushover API requires the emergency to be cancelled first before subsequent emergency notifications are received by Pushover from the same host. I don't think Proxmox can be configured to send a subsequent cancel by tag notification once the status changes to resolved (I added the "tags": "service=pve-emergency", to set a tag so that a manual cancel by tag is possible and it therefore isn't strictly necessary for emergency messages).

Others
You can use other optional Pushover API parametes such as device, sound, url, url_title and html by specifying them in the JSON format within the body.
  
Source: https://forum.proxmox.com/threads/api-examples-of-webhook-for-different-services.157865/#post-725269