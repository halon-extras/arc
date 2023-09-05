## Authenticated Received Chain (ARC) 
An ARC implementation based on [rfc8617](https://tools.ietf.org/html/rfc8617).

## Installation

Follow the [instructions](https://docs.halon.io/manual/comp_install.html#installation) in our manual to add our package repository and then run the below command.

### Ubuntu

```
apt-get install halon-extras-arc
```

### RHEL

```
yum install halon-extras-arc
```

## Example

```
import { dmarc } from "extras://dmarc";
import { AuthenticationResults } from "extras://authentication-results";
import { ARC } from "extras://arc";

$spf = spf_query($connection["remoteip"], $connection["helo"]["host"], $transaction["senderaddress"]["domain"]);
$dmarc = dmarc($arguments["mail"], $connection["remoteip"], $connection["helo"]["host"], $transaction["senderaddress"]["domain"]);

$chain = ARC::chainValidate($arguments["mail"]);
if ($chain["status"] == "pass" or $chain["status"] == "none")
{
	ARC::seal($arguments["mail"],
			"201805", "example.com", "arc",
			$chain,
			AuthenticationResults()
				->SPF($spf, $connection["helo"]["host"], $transaction["senderaddress"]["domain"], ["smtp.remote-ip" => $connection["remoteip"]])
				->DKIM($arguments["mail"])
				->DMARC($dmarc)
				->addMethod("arc", $chain["status"], ["header.oldest-pass" => $chain["oldestpass"] ?? "0"])
				->toString(),
			["id" => true]
		);
}
```
