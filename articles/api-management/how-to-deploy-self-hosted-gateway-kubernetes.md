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
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89500126"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Nasazení brány v místním prostředí v Kubernetes

Tento článek popisuje postup nasazení komponenty samoobslužné brány Azure API Management do clusteru Kubernetes.

## <a name="prerequisites"></a>Požadavky

- Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
- Vytvořte cluster Kubernetes.
   > [!TIP]
   > [Clustery s jedním uzlem](https://kubernetes.io/docs/setup/#learning-environment) fungují dobře pro účely vývoje a vyhodnocení. Používejte [Kubernetes certifikované](https://kubernetes.io/partners/#conformance) clustery s několika uzly místně nebo v cloudu pro produkční úlohy.
- [Zřízení prostředku brány v místním prostředí v instanci API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Nasazení do Kubernetes

1. V části **nasazení a infrastruktura** vyberte **brány** .
2. Vyberte prostředek samoobslužné brány, který chcete nasadit.
3. Vyberte **nasazení**.
4. K automatickému vygenerování přístupového tokenu v textovém poli **tokenu** na základě výchozích hodnot **vypršení platnosti** a **tajného klíče** . V případě potřeby vyberte hodnoty v jednom nebo obou ovládacích prvcích pro vygenerování nového tokenu.
5. V části **skripty pro nasazení** vyberte kartu **Kubernetes** .
6. Vyberte odkaz soubor **\<gateway-name\> . yml** a Stáhněte soubor YAML.
7. Výběrem ikony **kopírování** v pravém dolním rohu textového pole **nasadit** uložte `kubectl` příkazy do schránky.
8. Příkazy vložte do okna terminálu (nebo příkazu). První příkaz vytvoří tajný klíč Kubernetes, který obsahuje přístupový token vygenerovaný v kroku 4. Druhý příkaz použije konfigurační soubor stažený v kroku 6 do clusteru Kubernetes a očekává, že se soubor nachází v aktuálním adresáři.
9. Spusťte příkazy pro vytvoření nezbytných Kubernetes objektů ve [výchozím oboru názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) a spusťte z [Image kontejneru z image kontejneru](https://aka.ms/apim/sputnik/dhub) , kterou jste stáhli z Microsoft Container Registry.
10. Spusťte následující příkaz a ověřte, zda nasazení proběhlo úspěšně. Všimněte si, že může chvíli trvat, než se všechny objekty vytvoří a v případě, že se některé z nich mají inicializovat.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Spusťte následující příkaz, který zkontroluje, jestli se služba úspěšně vytvořila. Všimněte si, že IP adresy a porty služby se budou lišit.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Vraťte se na Azure Portal a vyberte **Přehled**.
13. Potvrďte, že **stav** zobrazuje zelený symbol zaškrtnutí následovaný počtem uzlů, který odpovídá počtu replik zadaných v souboru YAML. Tento stav znamená, že nasazená součást brány v místním prostředí úspěšně komunikuje se službou API Management a má normální "prezenční signál".

    ![Stav brány](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Spusťte <code>kubectl logs deployment/<gateway-name></code> příkaz pro zobrazení protokolů z náhodně vybraného prvku pod, pokud existuje více než jeden.
> Spusťte <code>kubectl logs -h</code> příkaz pro úplnou sadu možností příkazu, jako je například zobrazení protokolů pro konkrétní pod nebo kontejnerem.

## <a name="production-deployment-considerations"></a>Požadavky na produkční nasazení

### <a name="access-token"></a>Přístupový token
Bez platného přístupového tokenu nemůže místní brána získat přístup k datům konfigurace a stahovat je z koncového bodu přidružené služby API Management. Přístupový token může být platný po dobu maximálně 30 dnů. Je nutné ho znovu vygenerovat a cluster nakonfigurovaný s čerstvým tokenem, a to buď ručně, nebo prostřednictvím automatizace před tím, než vyprší jeho platnost.

Při automatizaci aktualizace tokenu použijte [tuto operaci rozhraní API pro správu](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) k vygenerování nového tokenu. Informace o správě Kubernetes tajných klíčů najdete na [webu Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret).

### <a name="namespace"></a>Obor názvů
Kubernetes [obory názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) vám pomůžou rozdělit jeden cluster mezi několik týmů, projektů nebo aplikací. Obory názvů poskytují obor pro prostředky a názvy. Můžou být přidružené k kvótě prostředků a zásadám řízení přístupu.

Azure Portal poskytuje příkazy pro vytváření prostředků brány v místním prostředí ve **výchozím** oboru názvů. Tento obor názvů se automaticky vytvoří, existuje v každém clusteru a nedá se odstranit.
Zvažte [Vytvoření a nasazení](https://kubernetesbyexample.com/ns/) brány s vlastním hostováním do samostatného oboru názvů v produkčním prostředí.

### <a name="number-of-replicas"></a>Počet replik
Minimální počet replik vhodných pro produkci je dva.

Ve výchozím nastavení je samoobslužná brána nasazená s [strategií](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)nasazení **RollingUpdate** . Zkontrolujte výchozí hodnoty a zvažte explicitní nastavení polí [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) a [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , zejména při použití vysokého počtu replik.

### <a name="container-resources"></a>Prostředky kontejneru
Ve výchozím nastavení soubor YAML, který je součástí Azure Portal, neurčuje požadavky prostředků kontejneru.

Není možné spolehlivě odhadnout a doporučit množství prostředků procesoru a paměti na kontejner a počet replik vyžadovaných pro podporu konkrétního zatížení. V současné době je k dispozici mnoho faktorů, například:

- Konkrétní hardware, na kterém je cluster spuštěn.
- Přítomnost a typ virtualizace.
- Počet a frekvence souběžných připojení klientů.
- Frekvence požadavků.
- Druh a počet konfigurovaných zásad.
- Velikost datové části a to, jestli jsou datové části ve vyrovnávací paměti nebo streamované.
- Latence back-endu služby

Doporučujeme nastavit požadavky na prostředky na dvě jádra a 2 GiB jako výchozí bod. Proveďte zátěžový test a navýšení nebo snížení kapacity na základě výsledků.

### <a name="container-image-tag"></a>Značka image kontejneru
Soubor YAML, který je součástí Azure Portal, používá **nejnovější** značku. Tato značka vždy odkazuje na nejnovější verzi image kontejneru samoobslužné brány.

Zvažte použití konkrétní značky verze v produkčním prostředí, aby nedocházelo k neúmyslnému upgradu na novější verzi.

Můžete [si stáhnout úplný seznam dostupných značek](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>Zásady DNS
Překlad názvů DNS hraje kritickou roli v možnosti brány v místním prostředí, která umožňuje připojení k závislostem v Azure a odesílání volání rozhraní API do back-endové služby.

Soubor YAML, který je součástí Azure Portal, používá výchozí zásady [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) . Tato zásada způsobuje, že se požadavky na překlad adres IP nevyřešily DNS clusteru, aby se předaly nadřazenému serveru DNS, který je zděděný z uzlu.

Další informace o překladu názvů v Kubernetes najdete na [webu Kubernetes](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service). Zvažte přizpůsobení [zásad DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) nebo [Konfigurace DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) podle potřeby pro vaši instalaci.

### <a name="external-traffic-policy"></a>Zásada externího provozu
Soubor YAML, který je k dispozici v poli Azure Portal sady `externalTrafficPolicy` u objektu [Service](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) na `Local` . Tím se zachová IP adresa volajícího (přístupná v [kontextu požadavku](api-management-policy-expressions.md#ContextVariables)) a zakáže vyrovnávání zatížení mezi uzly, což eliminuje směrování v síti, které to způsobilo. Pamatujte, že toto nastavení může způsobit asymetrickou distribuci provozu v nasazeních s nestejným počtem lusků brány na jeden uzel.

### <a name="custom-domain-names-and-ssl-certificates"></a>Vlastní názvy domén a certifikáty SSL

Pokud pro koncové body API Management používáte vlastní názvy domén, zejména pokud používáte vlastní název domény pro koncový bod správy, možná budete muset aktualizovat hodnotu `config.service.endpoint` v souboru **\<gateway-name\> . yaml** , aby se výchozí název domény nahradil názvem vlastní domény. Ujistěte se, že koncový bod správy je v clusteru Kubernetes k dispozici ze seznamu pod místní bránou.

Pokud v tomto scénáři není certifikát SSL, který je používán koncovým bodem správy, podepsaný známým certifikátem certifikační autority, je nutné zajistit, aby byl certifikát certifikační autority důvěryhodný v rámci samoobslužné brány.

### <a name="configuration-backup"></a>Zálohování konfigurace
Další informace o chování samoobslužné brány v přítomnosti dočasného výpadku připojení k Azure najdete v tématu [Přehled samoobslužné brány](self-hosted-gateway-overview.md#connectivity-to-azure).

Nakonfigurujte místní svazek úložiště pro kontejner samoobslužné brány, aby mohl uchovávat záložní kopii nejnovější stažené konfigurace. Pokud připojení nefunguje, svazek úložiště může po restartu použít záložní kopii. Cesta pro připojení svazku musí být <code>/apim/config</code> . Podívejte se na příklad na [GitHubu](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Další informace o službě Storage v Kubernetes najdete na [webu Kubernetes](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Místní protokoly a metriky
Samoobslužná brána odesílá telemetrii do [Azure monitor](api-management-howto-use-azure-monitor.md) a [Azure Application Insights](api-management-howto-app-insights.md) podle nastavení konfigurace v přidružené API Management službě.
Když se [připojení k Azure](self-hosted-gateway-overview.md#connectivity-to-azure) dočasně ztratí, tok telemetrie do Azure se přeruší a data se po dobu výpadku ztratí.
Zvažte [nastavení místního monitorování](how-to-configure-local-metrics-logs.md) , aby se zajistila možnost sledovat provoz rozhraní API a zabránit ztrátě telemetrie během výpadků připojení k Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o bráně pro samoobslužné hostování najdete v tématu [Přehled samoobslužné brány](self-hosted-gateway-overview.md).
