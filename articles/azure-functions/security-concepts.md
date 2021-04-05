---
title: Zabezpečení Azure Functions
description: Přečtěte si, jak zajistit, aby byl kód vaší funkce běžící v Azure lépe zabezpečený před běžnými útoky.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 351bdca7ff94b6c058b5ab62fd9c16d707e7dc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368485"
---
# <a name="securing-azure-functions"></a>Zabezpečení Azure Functions

V mnoha ohledech je plánování bezpečného vývoje, nasazení a provozu funkcí bez serveru stejné jako u všech webových nebo cloudových aplikací, které hostují. [Azure App Service](../app-service/index.yml) poskytuje infrastrukturu hostování pro aplikace Function App. Tento článek poskytuje strategie zabezpečení pro spuštění kódu funkce a způsob, jakým App Service vám může pomáhat zabezpečit vaše funkce. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Sadu doporučení zabezpečení, která se řídí [srovnávacím testem zabezpečení Azure](../security/benchmarks/overview.md), najdete v tématu [základní informace o zabezpečení Azure pro Azure Functions](security-baseline.md).

## <a name="secure-operation"></a>Zabezpečená operace 

V této části se můžete co nejdříve nakonfigurovat a spustit aplikaci Function App. 

### <a name="security-center"></a>Security Center

Security Center se integruje s aplikací Function App na portálu. Poskytuje zdarma rychlé posouzení potenciálních chyb zabezpečení souvisejících s konfigurací. Aplikace Function App spuštěné ve vyhrazeném plánu můžou k dalším nákladům použít také funkce zabezpečení Security Center v reálném čase. Další informace najdete v tématu [ochrana Azure App Service webových aplikací a rozhraní API](../security-center/defender-for-app-service-introduction.md). 

### <a name="log-and-monitor"></a>Protokolování a monitorování

Jedním ze způsobů detekce útoků je využití aktivity monitorování aktivity a analýzy protokolování. Funkce se integrují s Application Insights pro shromažďování dat o protokolech, výkonu a chybách pro aplikaci Function App. Application Insights automaticky detekuje anomálie výkonu a zahrnuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, jak se vaše funkce používají. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

Funkce se také integrují s protokoly Azure Monitor, které vám umožní konsolidovat protokoly aplikací funkcí se systémovými událostmi pro snazší analýzu. Nastavení diagnostiky můžete použít ke konfiguraci exportu protokolů a metrik platforem pro vaše funkce do cíle podle vašeho výběru, jako je například pracovní prostor Log Analytics. Další informace najdete v tématu [monitorování Azure Functions pomocí protokolů Azure monitor](functions-monitor-log-analytics.md). 

Pro detekci hrozeb na podnikové úrovni a automatizaci odpovědí Streamujte protokoly a události do pracovního prostoru Log Analytics. K tomuto pracovnímu prostoru pak můžete připojit Azure Sentinel. Další informace najdete v tématu [co je Azure Sentinel](../sentinel/overview.md).  

Další doporučení pro zabezpečení najdete v tématu základní informace o [zabezpečení Azure pro Azure Functions](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>Vyžadovat protokol HTTPS

Ve výchozím nastavení se klienti mohou připojit k koncovým bodům funkcí pomocí protokolu HTTP nebo HTTPS. Měli byste přesměrovat HTTP na HTTPs, protože HTTPS používá protokol SSL/TLS k zajištění zabezpečeného připojení, které je šifrované i ověřené. Další informace najdete v tématu [vymáhání protokolu HTTPS](../app-service/configure-ssl-bindings.md#enforce-https).

Pokud vyžadujete HTTPS, měli byste také vyžadovat nejnovější verzi TLS. Další informace najdete v tématu [vymáhání verzí TLS](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

Další informace najdete v tématu [zabezpečená připojení (TSL)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Přístupové klíče funkce

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Systémový klíč 

Specifická rozšíření můžou pro přístup k koncovým bodům Webhooku vyžadovat klíč spravovaný systémem. Systémové klíče jsou určeny pro koncové body funkcí specifické pro rozšíření, které jsou volány interními komponentami. Například [trigger Event Grid](functions-bindings-event-grid-trigger.md) vyžaduje, aby předplatné při volání koncového bodu triggeru používalo systémový klíč. Durable Functions používá taky systémové klíče k volání [rozhraní API pro rozšíření trvalých úloh](durable/durable-functions-http-api.md). 

Rozsah systémových klíčů závisí na rozšíření, ale obecně platí pro celou aplikaci Function App. Systémové klíče lze vytvořit pouze konkrétními rozšířeními a nemůžete explicitně nastavit jejich hodnoty. Podobně jako u jiných klíčů můžete vygenerovat novou hodnotu klíče z portálu nebo pomocí klíčových rozhraní API.

#### <a name="keys-comparison"></a>Porovnání klíčů

Následující tabulka porovnává použití různých druhů přístupových klíčů:

| Akce                                        | Obor                    | Platné klíče         |
|-----------------------------------------------|--------------------------|--------------------|
| Spustit funkci                            | Konkrétní funkce        | Funkce           |
| Spustit funkci                            | Libovolná funkce             | Funkce nebo hostitel   |
| Volání koncového bodu správce                        | Aplikace funkcí             | Hostitel (jenom Master) |
| Volání rozhraní API pro rozšíření trvalých úloh              | Aplikace Function App<sup>1</sup> | Systém<sup>2</sup> |
| Volání Webhooku specifického pro rozšíření (interní) | Aplikace Function App<sup>1</sup> | systém<sup>2</sup> |

<sup>1</sup> Rozsah stanovený rozšířením.  
<sup>2</sup> . Konkrétní názvy nastavené podle přípony.

Další informace o přístupových klíčích najdete v [článku vázání triggeru http](functions-bindings-http-webhook-trigger.md#obtaining-keys).


#### <a name="secret-repositories"></a>Tajná úložiště

Ve výchozím nastavení se klíče ukládají do kontejneru úložiště objektů BLOB v účtu poskytnutém `AzureWebJobsStorage` nastavením. Pomocí určitých nastavení aplikace můžete toto chování přepsat a klíče ukládat v jiném umístění.

|Umístění  |Nastavení | Hodnota | Popis  |
|---------|---------|---------|---------|
|Jiný účet úložiště     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Ukládá klíče v úložišti objektů BLOB druhého účtu úložiště na základě zadané adresy URL SAS. Klíče se šifrují předtím, než se uloží pomocí tajného klíče jedinečného pro vaši aplikaci Function App. |
|Systém souborů   | `AzureWebJobsSecretStorageType`   |  `files`       | Klíče se v systému souborů uchovávají, šifrované před úložištěm s použitím tajného klíče jedinečného pro vaši aplikaci Function App. |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | V trezoru musí být zásady přístupu, které odpovídají spravované identitě prostředku hostování. Zásada přístupu by měla identitě udělit následující skrytá oprávnění: `Get` , `Set` , `List` a `Delete` . <br/>Při místním spuštění se používá identita vývojáře a nastavení se musí nacházet vlocal.settings.jsv [ souboru](functions-run-local.md#local-settings-file). | 
|Tajné klíče Kubernetes  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (volitelné) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Podporováno pouze při spuštění modulu runtime Functions v Kubernetes. Pokud `AzureWebJobsKubernetesSecretName` není nastaveno, úložiště je považováno za jen pro čtení. V takovém případě musí být hodnoty generovány před nasazením. Azure Functions Core Tools generuje hodnoty automaticky při nasazení do Kubernetes.|

### <a name="authenticationauthorization"></a>Ověřování/autorizace

I když můžou klíče funkcí poskytnout nějaké zmírnění nežádoucího přístupu, jediným způsobem, jak zajistit zabezpečení vašich koncových bodů funkcí, je implementace pozitivního ověřování klientů, kteří přistupují k vašim funkcím. Na základě identity pak můžete provádět rozhodnutí o autorizaci.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Oprávnění

Jako u jakékoli aplikace nebo služby je cílem spustit aplikaci Function App s nejnižšími možnými oprávněními. 

#### <a name="user-management-permissions"></a>Oprávnění správy uživatelů

Funkce podporují integrované [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md). Role Azure podporované funkcemi jsou [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor), [vlastník](../role-based-access-control/built-in-roles.md#owner)a [Čtenář](../role-based-access-control/built-in-roles.md#owner). 

Oprávnění jsou platná na úrovni aplikace Function App. Role přispěvatele je nutná k provádění většiny úkolů na úrovni aplikace Function App. Aplikace Function App může odstranit pouze role vlastníka. 

#### <a name="organize-functions-by-privilege"></a>Uspořádání funkcí podle oprávnění 

Připojovací řetězce a další přihlašovací údaje uložené v nastavení aplikace poskytují všem funkcím aplikace Function App stejnou sadu oprávnění v přidruženém prostředku. Pomocí přesunu funkcí, které tyto přihlašovací údaje nepoužívají, do samostatné aplikace Function App zvažte minimalizaci počtu funkcí s přístupem k určitým přihlašovacím údajům. K předávání dat mezi funkcemi v různých aplikacích funkcí můžete vždy použít techniky, jako je například [řetězení funkcí](/learn/modules/chain-azure-functions-data-using-bindings/) .  

#### <a name="managed-identities"></a>Spravované identity

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Spravované identity lze použít místo tajných kódů pro připojení z některých triggerů a vazeb. Viz [připojení založená na identitách](#identity-based-connections).

Další informace najdete v tématu [Jak používat spravované identity pro App Service a Azure Functions](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>Omezení přístupu CORS

[Sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) je způsob, jak umožnit webovým aplikacím běžícím v jiné doméně vytvářet požadavky na koncové body triggeru http. App Service poskytuje integrovanou podporu pro předání požadovaných hlaviček CORS v požadavcích HTTP. Pravidla CORS jsou definovaná na úrovni aplikace Function App.  

I když se rozhodnete použít zástupný znak, který umožňuje všem webům přístup k vašemu koncovému bodu. To ale předchází účelu CORS, což znamená, aby se zabránilo útokům na skriptování mezi weby. Místo toho přidejte samostatnou položku CORS pro doménu každé webové aplikace, která musí mít přístup ke koncovému bodu. 

### <a name="managing-secrets"></a>Správa tajných klíčů 

Aby bylo možné se připojit k různým službám a prostředkům, musí být váš kód spuštěn, aplikace Function App musí mít přístup k tajným klíčům, jako jsou připojovací řetězce a klíče služby. Tato část popisuje, jak ukládat tajné klíče vyžadované funkcemi.

Nikdy neukládejte tajné klíče do kódu funkce. 

#### <a name="application-settings"></a>Nastavení aplikace

Ve výchozím nastavení ukládáte připojovací řetězce a tajné klíče, které používá aplikace Function App, a vazby jako nastavení aplikace. Tyto přihlašovací údaje jsou k dispozici pro váš kód funkce i různé vazby používané funkcí. Název nastavení aplikace (klíč) se používá k načtení skutečné hodnoty, která je tajná. 

Například každá aplikace Function App vyžaduje přidružený účet úložiště, který je používán modulem runtime. Ve výchozím nastavení je připojení k tomuto účtu úložiště uložené v nastavení aplikace s názvem `AzureWebJobsStorage` .

Nastavení aplikace a připojovací řetězce se ukládají v Azure jako šifrované. Dešifrují se jenom předtím, než se vloží do paměti procesu aplikace při spuštění aplikace. Šifrovací klíče se pravidelně otáčí. Pokud raději spravujete zabezpečené úložiště vašich tajných kódů, nastavení aplikace by mělo být místo odkazů na Azure Key Vault. 

Při vývoji funkcí v místním počítači můžete také ve výchozím nastavení šifrovat nastavení v local.settings.jssouboru. Další informace najdete `IsEncrypted` v tématu vlastnost v [souboru místních nastavení](functions-run-local.md#local-settings-file).  

#### <a name="key-vault-references"></a>Odkazy na Key Vault

I když jsou nastavení aplikace dostačující pro většinu funkcí, možná budete chtít sdílet stejné tajné kódy napříč více službami. V tomto případě redundantní úložiště tajných klíčů vede k více potenciálním ohrožením zabezpečení. Bezpečnější je přístup ke službě centrálního tajného úložiště a místo samotných tajných klíčů používat odkazy na tuto službu.      

[Azure Key Vault](../key-vault/general/overview.md) je služba, která poskytuje centralizovanou správu tajných kódů s úplnou kontrolou zásad přístupu a historie auditu. Odkaz na Key Vault můžete použít místo připojovacího řetězce nebo klíče v nastavení aplikace. Další informace najdete v tématu [použití Key Vault odkazů pro App Service a Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="identity-based-connections"></a>Připojení založená na identitách

Místo tajných kódů, které se připojují k některým prostředkům, se dají použít identity. To má výhodu, že není potřeba spravovat tajný kód a poskytuje přesnější řízení přístupu a auditování. 

Při psaní kódu, který vytváří připojení ke [službám Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), se můžete rozhodnout použít identitu místo tajného nebo připojovacího řetězce. Podrobnosti o obou metodách připojení jsou pokryté v dokumentaci pro každou službu.

Některá Azure Functions Trigger a rozšíření vazby můžou být nakonfigurované pomocí připojení založeného na identitě. V současné době zahrnuje rozšíření [Azure Blob](./functions-bindings-storage-blob.md) a [Azure Queue](./functions-bindings-storage-queue.md) Extensions. Informace o tom, jak nakonfigurovat tato rozšíření pro použití identity, najdete [v tématu použití připojení na základě identity v Azure Functions](./functions-reference.md#configure-an-identity-based-connection).

### <a name="set-usage-quotas"></a>Nastavení kvót využití

Zvažte nastavení kvóty využití pro funkce spuštěné v plánu spotřeby. Když nastavíte denní limit GB u součtu na celkový počet spuštění funkcí ve vaší aplikaci Function App, po dosažení limitu se spuštění zastaví. To může potenciálně přispět k zmírnění škodlivého kódu, který spouští vaše funkce. Pokud se chcete dozvědět, jak odhadnout spotřebu pro vaše funkce, přečtěte si téma [odhad nákladů na plán spotřeby](functions-consumption-costs.md). 

### <a name="data-validation"></a>Ověřování dat

Aktivační události a vazby používané vašimi funkcemi neposkytují žádné další ověření dat. Váš kód musí ověřit všechna data přijatá z triggeru nebo vstupní vazby. Pokud dojde k ohrožení proti nadřazené službě, nechcete, aby se vstupy z vašich funkcí prošly neověřenými vstupy. Pokud například vaše funkce ukládá data z fronty Azure Storage v relační databázi, je nutné ověřit data a parametrizovat příkazy, aby nedocházelo k útokům prostřednictvím injektáže SQL. 

Nepředpokládáme, že data přicházející do vaší funkce již byla ověřena nebo upravena. Je také vhodné ověřit, zda jsou data zapsaná do výstupních vazeb platná. 

### <a name="handle-errors"></a>Ošetření chyb

I když se zdá, že se jedná o základní, je důležité, abyste ve svých funkcích napsali dobré zpracování chyb. Neošetřené chyby bublinují do hostitele a jsou zpracovávány modulem runtime. Různé vazby zpracovávají zpracování chyb odlišně. Další informace najdete v tématu [Azure Functions zpracování chyb](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Zakázat vzdálené ladění

Ujistěte se, že je vzdálené ladění zakázané, s výjimkou případů, kdy aktivně ladíte vaše funkce. Vzdálené ladění můžete zakázat na kartě **Obecné nastavení** **Konfigurace** Function App na portálu. 

### <a name="restrict-cors-access"></a>Omezení přístupu CORS

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

V seznamu povolených původních míst nepoužívejte zástupné znaky. Místo toho uveďte konkrétní domény, ze kterých očekáváte získání požadavků.

### <a name="store-data-encrypted"></a>Zašifrované údaje úložiště

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Zabezpečené nasazení

Azure Functions nástrojů integrace usnadňuje publikování kódu projektu místní funkce do Azure. Je důležité pochopit, jak nasazení funguje při zvažování zabezpečení Azure Functions topologie.   

### <a name="deployment-credentials"></a>Přihlašovací údaje pro nasazení

App Service nasazení vyžadují sadu přihlašovacích údajů pro nasazení. Tyto přihlašovací údaje pro nasazení se používají k zabezpečení vašich nasazení aplikací Function App. Přihlašovací údaje nasazení jsou spravovány App Service platformou a jsou zašifrované v klidovém stavu. 

K dispozici jsou dva druhy přihlašovacích údajů pro nasazení:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

V tuto chvíli se Key Vault pro přihlašovací údaje k nasazení nepodporují. Další informace o správě přihlašovacích údajů nasazení najdete v tématu [konfigurace přihlašovacích údajů nasazení pro Azure App Service](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>Zakázat FTP

Každá aplikace Function App má ve výchozím nastavení povolený koncový bod FTP. Ke koncovému bodu FTP se dostanete pomocí přihlašovacích údajů pro nasazení. 

Protokol FTP není doporučen pro nasazení kódu funkce. Nasazení FTP jsou ruční a vyžadují synchronizaci triggerů. Další informace najdete v tématu [nasazení FTP](functions-deployment-technologies.md#ftp). 

Pokud neplánujete používat FTP, měli byste ho zakázat na portálu. Pokud se rozhodnete použít FTP, měli byste [vyhovět FTPS](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Zabezpečení koncového bodu SCM

Každá aplikace Function App má odpovídající `scm` koncový bod služby, který používá služba Advanced Tools (Kudu) pro nasazení a další rozšíření App Service [lokality](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). Koncový bod SCM pro aplikaci Function App je vždycky adresa URL ve formuláři `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` . Pokud k zabezpečení svých funkcí používáte izolaci sítě, musíte taky pro tento koncový bod použít účet. 

Když máte samostatný koncový bod SCM, můžete řídit nasazení a další pokročilé nástroje pro aplikace Function App, které jsou izolované nebo spuštěné ve virtuální síti. Koncový bod SCM podporuje základní ověřování (pomocí přihlašovacích údajů pro nasazení) a jednotné přihlašování s přihlašovacími údaji Azure Portal. Další informace najdete v tématu [přístup ke službě Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Průběžné ověřování zabezpečení

Vzhledem k tomu, že je potřeba zabezpečení považovat za každý krok v procesu vývoje, má smysl taky implementovat ověřování zabezpečení v prostředí průběžného nasazování. To se někdy označuje jako DevSecOps. Použití Azure DevOps pro váš kanál nasazení vám umožní integrovat ověřování do procesu nasazení. Další informace najdete v tématu [informace o tom, jak přidat průběžné ověřování zabezpečení do kanálu CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Zabezpečení sítě

Omezení přístupu k síti do aplikace Function App vám umožní řídit, kdo má přístup k koncovým bodům funkcí. Služba Functions využívá infrastrukturu App Service, aby umožnila vašim funkcím přístup k prostředkům bez použití adres, které jsou směrovatelný v Internetu, nebo k omezení přístupu k Internetu na koncový bod funkce. Další informace o těchto možnostech sítě najdete v tématu [možnosti Azure Functions sítě](functions-networking-options.md).

### <a name="set-access-restrictions"></a>Nastavení omezení přístupu

Omezení přístupu umožňují definovat seznamy pravidel pro povolení nebo odepření řízení provozu do vaší aplikace. Pravidla se vyhodnocují v pořadí podle priority. Pokud nejsou definována žádná pravidla, bude aplikace přijímat přenosy z libovolné adresy. Další informace najdete v tématu [omezení přístupu Azure App Service #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>Privátní přístup k webu

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Nasazení aplikace Function App v izolaci

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Použití služby brány

Služby brány, jako je například [azure Application Gateway](../application-gateway/overview.md) a [přední dveře Azure](../frontdoor/front-door-overview.md) , vám umožní nastavit firewall webových aplikací (WAF). Pravidla WAF se používají k monitorování nebo blokování zjištěných útoků, které poskytují další vrstvu ochrany pro vaše funkce. K nastavení WAF je potřeba, aby aplikace Function App běžela v pomocném mechanismu nebo pomocí privátních koncových bodů (Preview). Další informace najdete v tématu [použití privátních koncových bodů](../app-service/networking/private-endpoint.md).    

## <a name="next-steps"></a>Další kroky

+ [Základní hodnoty zabezpečení Azure pro Azure Functions](security-baseline.md)
+ [Diagnostika Azure Functions](functions-diagnostics.md)
