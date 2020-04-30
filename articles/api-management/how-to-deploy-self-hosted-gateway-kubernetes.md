---
title: Nasazení samoobslužné brány do Kubernetes | Microsoft Docs
description: Přečtěte si, jak nasadit komponentu samoobslužné brány Azure API Management do Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205102"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Nasazení samoobslužné brány do Kubernetes

Tento článek popisuje postup nasazení součásti samoobslužné brány Azure API Management do clusteru Kubernetes.

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Vytvořte cluster Kubernetes.
> [!TIP]
> [Clustery s jedním uzlem](https://kubernetes.io/docs/setup/#learning-environment) fungují dobře pro účely vývoje a vyhodnocení. Používejte [Kubernetes certifikované](https://kubernetes.io/partners/#conformance) clustery s několika uzly místně nebo v cloudu pro produkční úlohy.
- [Zřízení prostředku brány v místním prostředí v instanci API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Nasazení do Kubernetes

1. V části **nasazení a infrastruktura**vyberte **brány** .
2. Vyberte prostředek samoobslužné brány, který chcete nasadit.
3. Vyberte **nasazení**.
4. Všimněte si, že přístupový token v textovém poli **token** byl automaticky vygenerován za použití výchozích hodnot **vypršení platnosti** a **tajného klíče** . V případě potřeby vyberte požadované hodnoty v jednom nebo obou ovládacích prvcích pro vygenerování nového tokenu.
5. V části skripty pro **nasazení**vyberte kartu **Kubernetes** .
6. Klikněte na odkaz **<brána – název> soubor. yml** a Stáhněte si soubor YAML.
7. Vyberte možnost **Kopírovat** ikonu umístěnou v pravém dolním rohu textového pole **nasadit** a uložte `kubectl` příkazy do schránky.
8. Příkazy vložte do okna terminálu (nebo příkazu). První příkaz vytvoří tajný klíč Kubernetes s přístupovým tokenem generovaným v kroku 4. Druhý příkaz použije konfigurační soubor stažený v kroku 6 do clusteru Kubernetes a očekává, že se soubor nachází v aktuálním adresáři.
9. Spusťte příkazy pro vytvoření nezbytných Kubernetes objektů ve [výchozím oboru názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) a spusťte místní bránu pod [imagí z image kontejneru](https://aka.ms/apim/sputnik/dhub) staženou z Microsoft Container Registry.
10. Spusťte příkaz níže zobrazený pro kontrolu úspěšného nasazení. Všimněte si, že může trvat nějakou dobu, než se všechny objekty vytvoří a v případě, že se mají inicializovat.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. Spusťte příkaz níže zobrazený pro kontrolu, jestli se služba úspěšně vytvořila. Všimněte si, že IP adresy a porty služby se budou lišit.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Vraťte se na Azure Portal a vyberte **Přehled**.
13. **Stav** ukazující zelenou ikonu zaškrtnutí následovaný počtem uzlů, který odpovídá počtu replik zadaných v souboru YAML, potvrzuje, že nasazené místně hostované brány úspěšně komunikují se službou API Management a mají normální "prezenční signál".

![stav brány](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Spustí <code>kubectl logs deployment/<gateway-name></code> příkaz pro zobrazení protokolů z náhodně vybraného pod, pokud existuje více než jeden.
> Provést <code>kubectl logs -h</code> pro úplnou sadu možností příkazu, například zobrazení protokolů pro konkrétní podmnožinu nebo kontejner.

## <a name="production-deployment-considerations"></a>Požadavky na produkční nasazení

### <a name="access-token"></a>Přístupový token
Bez platného přístupového tokenu brány k místnímu přístupu nemá přístup ke konfiguraci z koncového bodu konfiguračních dat přidružené služby API Management a nestáhne je. Přístupový token může být platný po dobu maximálně 30 dnů. Je nutné ho znovu vygenerovat a cluster s čerstvým tokenem nakonfigurovaný buď ručně, nebo prostřednictvím automatizace před tím, než vyprší jeho platnost. Při automatizaci aktualizace tokenů použít tuto [operaci](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) rozhraní API pro správu k vygenerování nového tokenu. V tomto [odkazu](https://kubernetes.io/docs/concepts/configuration/secret) najdete informace o správě Kubernetes tajných klíčů.

### <a name="namespace"></a>Obor názvů
Kubernetes [obory názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) vám pomůžou rozdělit jeden cluster mezi několik týmů, projektů nebo aplikací. Obory názvů poskytují obor pro prostředky a názvy a dají se přidružit k kvótě prostředků a zásadám řízení přístupu.
Příkazy, které jsou k dispozici v Azure Portal vytváření prostředků samoobslužné brány ve **výchozím** oboru názvů, který je automaticky vytvořen, existují v každém clusteru a nelze je odstranit.
Zvažte [Vytvoření a nasazení](https://kubernetesbyexample.com/ns/) brány s vlastním hostováním do samostatného oboru názvů v produkčním prostředí.

### <a name="number-of-replicas"></a>Počet replik
Minimální počet replik, které jsou vhodné v produkčním prostředí, jsou dvě.

Ve výchozím nastavení je samoobslužná brána nasazená s [strategií](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)nasazení **RollingUpdate** . Zkontrolujte výchozí hodnoty a zvažte explicitní nastavení polí [**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) a [**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , zejména při použití vysokého počtu replik.

### <a name="container-resources"></a>Prostředky kontejneru
Ve výchozím nastavení soubor YAML, který je součástí Azure Portal, neurčuje požadavky prostředků kontejneru.

Není možné spolehlivě předpovědět a doporučit množství prostředků procesoru a paměti na kontejner a počet replik vyžadovaných k podpoře konkrétního zatížení z důvodu mnoha faktorů při hraní, např.:

- Konkrétní hardware, na kterém je cluster spuštěný
- Přítomnost a typ virtualizace
- Počet a frekvence souběžných připojení klientů
- Počet požadavků
- Druh a počet konfigurovaných zásad
- Velikost datové části a pokud jsou datové části ve vyrovnávací paměti nebo streamované
- Latence back-endu služby

Doporučujeme nastavit požadavek na prostředky na 2 jádra a 2 GiB jako výchozí bod, provést zátěžový test a škálovat nahoru/dolů nebo dolů na základě výsledků.

### <a name="container-image-tag"></a>Značka image kontejneru
Soubor YAML, který je součástí Azure Portal, používá **nejnovější** značku, která vždy odkazuje na nejnovější verzi image kontejneru v samoobslužné bráně.

Zvažte použití konkrétní značky verze v produkčním prostředí, aby nedocházelo k neúmyslnému upgradu na novější verzi.

Pomocí tohoto [odkazu](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) můžete zobrazit úplný seznam dostupných značek.

### <a name="dns-policy"></a>Zásady DNS
Překlad názvů DNS hraje důležitou roli v rámci možnosti brány v místním prostředí, která umožňuje připojení k závislostem v Azure a odesílání volání rozhraní API do back-endové služby.

Soubor YAML, který je součástí Azure Portal, používá výchozí zásadu [**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) , která způsobí, že se požadavky na překlad IP adres nevyřešily DNS clusteru, aby se předaly do NADŘAZENÉHO serveru DNS zděděného z uzlu.

Pomocí tohoto [odkazu](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) se dozvíte víc o překladu názvů v Kubernetes a zvažte přizpůsobení konfigurace [zásad DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) nebo D[NS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) podle potřeby pro instalaci.

### <a name="configuration-backup"></a>Zálohování konfigurace
Na tomto [odkazu](self-hosted-gateway-overview.md#connectivity-to-azure) najdete informace o chování samoobslužné brány v přítomnosti dočasného výpadku připojení k Azure.
Nakonfigurujte místní svazek úložiště pro kontejner samoobslužné brány, aby mohl uchovávat záložní kopii nejnovější stažené konfigurace, a pokud je připojení mimo provoz, použijte ho při restartování. Cesta pro připojení svazku musí být <code>/apim/config</code>. [Tady se můžete](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)podívat na příklad.
Další informace o úložišti v Kubernetes získáte pomocí tohoto [odkazu](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Místní protokoly a metriky
Samoobslužná brána odesílá telemetrii do [Azure monitor](api-management-howto-use-azure-monitor.md) a [Azure Application Insights](api-management-howto-app-insights.md) podle nastavení konfigurace v přidružené API Management službě.
Když se [připojení k Azure](self-hosted-gateway-overview.md#connectivity-to-azure) dočasně ztratí, tok telemetrie do Azure se přeruší a data se po dobu výpadku ztratí.
Zvažte [nastavení místního monitorování](how-to-configure-local-metrics-logs.md) a zajistěte tak schopnost sledovat provoz rozhraní API a zabránit ztrátě telemetrie během výpadků připojení k Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md) .