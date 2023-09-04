## Authenticated Received Chain (ARC) 
An ARC implementation based on [rfc8617](https://tools.ietf.org/html/rfc8617).

```
import { dmarc } from "extras://dmarc";
import { AuthenticationResults } from "extras://authentication-results";
import { ARC } from "arc/arc.hsl";

$spf = spf_query($connection["remoteip"], $connection["helo"]["host"], $transaction["senderaddress"]["domain"]);
$dmarc = dmarc($arguments["mail"], $connection["remoteip"], $connection["helo"]["host"], $transaction["senderaddress"]["domain"]);

$chain = ARC::chainValidate($arguments["mail"]);
if ($chain["status"] == "pass" or $chain["status"] == "none")
{
	ARC::seal($arguments["mail"],
			"201805", "example.com", "pki:arc",
			$chain,
			AuthenticationResults()
				->SPF($spf, $connection["helo"]["host"], $transaction["senderaddress"]["domain"], ["smtp.remote-ip" => $connection["remoteip"]])
				->DKIM($arguments["mail"])
				->DMARC($dmarc)
				->addMethod("arc", $chain["status"], ["header.oldest-pass" => $chain["oldestpass"] ?? "0"])
				->toString()
		);
}
```
