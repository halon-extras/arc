## Authenticated Received Chain (ARC) 
An ARC implementation based on [draft-ietf-dmarc-arc-protocol-18](https://tools.ietf.org/html/draft-ietf-dmarc-arc-protocol-18).

```
import { ARC } from "arc/arc.hsl";
import { AuthenticationResults } from "authentication-results/authentication-results.hsl";

$chain = ARC::chainValidate($mime);
if ($chain["status"] == "pass" or $chain["status"] == "none")
{
	ARC::seal($mime,
			"201805", "example.com", "pki:arc",
			$chain,
			AuthenticationResults()
				->SPF(["smtp.remote-ip" => $senderip])
				->DKIM()
				->DMARC()
				->addMethod("arc", $chain["status"], ["header.oldest-pass" => $chain["oldestpass"] ?? "0"])
				->toString()
		);
}
```
