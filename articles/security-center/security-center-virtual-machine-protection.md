---
title: Ochrana vašich počítačů a aplikací ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje doporučení ve službě Security Center, které vám pomůžou chránit virtuální počítače a počítače a webové aplikace a služby App Service Environment.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: v-mohabe
ms.openlocfilehash: cb6025b47aec50121a0f96f7b6cba7ad726db291
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907957"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Ochrana vašich počítačů a aplikací ve službě Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure, serverů mimo Azure a virtuálních počítačů. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol. Doporučení platí pro typy prostředků Azure: virtuální počítače (VM) a počítačů, aplikací, sítí, SQL a identit a přístupů.

Tento článek se zabývá doporučení, která platí pro počítače a aplikace.

## <a name="monitoring-security-health"></a>Sledování stavu zabezpečení
Můžete monitorovat stav zabezpečení vašich prostředků na **Security Center – přehled** řídicího panelu. **Prostředky** část obsahuje počet problémů zjištěných a stavu zabezpečení pro každý typ prostředku.

Seznam všech problémů můžete zobrazit tak, že vyberete **doporučení**. Další informace o tom, jak používat doporučení, najdete v části [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

Úplný seznam výpočetní prostředky a aplikace služeb doporučení najdete v tématu [doporučení](security-center-virtual-machine-protection.md#compute-and-app-recommendations).

Chcete-li pokračovat, vyberte **výpočty a aplikace** pod **prostředky** nebo hlavní nabídce služby Security Center.
![Řídicí panel Security Center](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorování výpočetních a aplikačních služeb
V části **výpočetní & aplikace**jsou k dispozici následující karty:

- **Přehled:** Monitorování a doporučení identifikovaná službou Security Center
- **Virtuální počítače a počítače:** Seznam virtuálních počítačů, počítačů a aktuální stav jejich zabezpečení
- **Cloudové služby:** Seznam webových a pracovních rolí monitorovaných službou Security Center
- **App Services**: seznam prostředí App Service a aktuální stav zabezpečení každého.
- **Kontejnery (Preview)** : Seznam kontejnerů hostovaných na počítačích se systémem IaaS Linux a posouzení zabezpečení jejich konfigurací Docker.
- **Výpočetní prostředky (Preview)** : seznam doporučení pro výpočetní prostředky, například Service Fabric clusterů a centra událostí.

Pokud chcete pokračovat, vyberte **compute & aplikace** v části **hygiena zabezpečení prostředků**.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

Na každé kartě můžete mít několik částí a v každé části můžete vybrat jednotlivé možnosti, abyste zobrazili další podrobnosti o doporučeném postupu k vyřešení konkrétního problému.

### Nemonitorované virtuální počítače a počítače <a name="unmonitored-vms-and-computers"></a>
Virtuální počítač nebo počítač je nemonitorovaný službou Security Center, pokud počítač není spuštěný rozšíření Microsoft Monitoring Agent. Počítač může mít již nainstalovaný místní agent, například agenta OMS Direct nebo Agent System Center Operations Manager. Počítače s těmito agenty se identifikují jako nemonitorované, protože tyto agenty plně nepodporuje ve službě Security Center. Pokud chcete naplno využívat všechny schopnosti služby Security Center, potřebujete rozšíření Microsoft Monitoring Agent.

Rozšíření můžete nainstalovat na nemonitorovaný virtuální počítač nebo počítač kromě již nainstalovanému místnímu agentu. Nakonfigurujte oba agenty stejným způsobem a připojte je ke stejnému pracovnímu prostoru. Tím umožníte službě Security Center pracovat s rozšířením Microsoft Monitoring Agent a shromažďovat data. Pokyny k instalaci rozšíření Microsoft Monitoring Agent najdete v tématu popisujícím [povolení rozšíření virtuálního počítače](../azure-monitor/learn/quick-collect-azurevm.md).

Další informace o důvodech, proč se službě Security Center nedaří úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování, najdete v tématu popisujícím [problémy s monitorováním stavu agenta](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Doporučení
Tato část obsahuje sadu doporučení jednotlivých virtuálních počítačů a počítačů, webových a pracovních rolí, Azure App Service Web Apps a Azure App Service Environment, která monitoruje Security Center. První sloupec uvádí doporučení. Druhý sloupec zobrazuje celkový počet prostředků, které jsou ovlivněny tímto doporučením. Třetí sloupec zobrazuje závažnost problému.

Každé doporučení obsahuje sadu akcí, které můžete provést po výběru. Pokud například vyberete **chybějící aktualizace systému**, zobrazí se počet virtuálních počítačů a počítačů, ve kterých chybí opravy, a závažnost chybějící aktualizace.

**Nainstalovat aktualizace systému** obsahuje souhrn důležitých aktualizací ve formátu grafu, jeden pro Windows a jeden pro Linux. Druhá část obsahuje tabulku s následujícími informacemi:

- **NÁZEV**: Název chybějící aktualizace
- **POČET Virtuálních počítačů & počítačů**: Celkový počet virtuálních počítačů a počítačů, ve kterých chybí Tato aktualizace.
- **ZÁVAŽNOST AKTUALIZACE**: Popisuje závažnost tohoto konkrétního doporučení:

    - **Kritické**: Existuje chyba s smysluplným prostředkem (aplikace, virtuální počítač nebo skupina zabezpečení sítě) a vyžaduje pozornost.
    - **Důležité**informace: K dokončení procesu nebo odstranění ohrožení zabezpečení se vyžadují nekritické nebo další kroky.
    - **Střední**: Ohrožení zabezpečení by se mělo řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)


- **STAV**: Aktuální stav doporučení:

    - **Otevřete**: Doporučení ještě není vyřešené.
    - **Probíhá**: Doporučení se v tuto chvíli aplikuje na tyto prostředky a nevyžaduje žádnou akci.
    - **Vyřešeno**: Doporučení již bylo dokončeno. (Pokud byl problém vyřešen, položka je vyšedlá.)

Pokud chcete zobrazit podrobnosti o doporučení, klikněte na název chybějící aktualizace v seznamu.


> [!NOTE]
> Bezpečnostní doporučení uvedená tady jsou stejné jako ty v rámci **doporučení** dlaždici. Zobrazit [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) Další informace o tom, jak řešit doporučení.
>
>

### <a name="vms-and-computers"></a>Virtuální počítače a počítače
Část virtuálních počítačů a počítačů poskytuje přehled o všech doporučení pro virtuální počítač a počítač. Každý sloupec představuje jednu sadu doporučení.

![Doporučení pro virtuální počítač a počítač](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Existují čtyři typy ikon v tomto seznamu:

![Počítač umístěný mimo Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Počítač mimo Azure.

![Virtuální počítač Azure Resource Manageru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Virtuální počítač Azure Resource Manageru.

![Virtuální počítač Azure Classic](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Virtuální počítač Azure Classic.


![Virtuální počítače uvedené v pracovním prostoru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuální počítače identifikované pouze z pracovního prostoru, který je součástí zobrazeného předplatného. To zahrnuje virtuální počítače z jiných předplatných, které jsou součástí pracovního prostoru v tomto předplatném, a virtuální počítače, které byly nainstalovány s Operations Manager přímým agentem, a nemají žádné ID prostředku.

Ikona, který se zobrazí pod každým doporučením, pomáhá rychle identifikovat virtuální počítač a počítač, který vyžaduje pozornost a typ doporučení se jedná. Pomocí filtrů můžete také Hledat v seznamu podle **typu prostředku** a podle závažnosti.

Pokud chcete přejít k podrobnostem o zabezpečení pro každý virtuální počítač, klikněte na virtuální počítač.
Tady vidíte podrobné informace o zabezpečení pro virtuální počítač nebo počítač. V dolní části vidíte doporučenou akci a závažnost jednotlivých problémů.
![Cloudové služby](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Cloud Services
Pro cloudové služby se vytvoří doporučení, když je verze operačního systému zastaralá.

![Cloud Services](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

V případě, kdy máte doporučení (což není případ předchozího příkladu) budete muset postupovat podle kroků v doporučení a aktualizovat verzi operačního systému. Když je k dispozici aktualizace, obdržíte výstrahu (červenou nebo oranžovou – v závislosti na závažnosti problému). Když vyberete tuto výstrahu v WebRole1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS) nebo WorkerRole1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS), zobrazí se další podrobnosti o tomto doporučení.

Pokud chcete zobrazit podrobnější vysvětlení tohoto doporučení, klikněte na **Aktualizovat verzi operačního systému** ve sloupci **POPIS**.



![Aktualizace verze operačního systému](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Aplikační služby
Pokud chcete zobrazit informace o App Service, musíte ve svém předplatném povolit App Service. Pokyny, jak tuto funkci povolit, najdete v tématu [ochrana App Service pomocí Azure Security Center](security-center-app-services.md).
[!NOTE]
> Monitorování služby App Service je ve verzi preview a je k dispozici jenom na úrovni Standard služby Security Center.


V části **App services**najdete seznam vaší služby App service Environment a provést shrnutí stavu na základě posouzení Security Center.

![Aplikační služby](./media/security-center-virtual-machine-recommendations/app-services.png)

Existují tři typy ikon v tomto seznamu:

![Prostředí App services](./media/security-center-virtual-machine-recommendations/ase.png) Prostředí App services.

![Webová aplikace](./media/security-center-virtual-machine-recommendations/web-app.png) Webová aplikace.

![Aplikace – funkce](./media/security-center-virtual-machine-recommendations/function-app.png) Použití funkce.

1. Vyberte webovou aplikaci. Souhrnné zobrazení otevřete tři karty:

   - **Doporučení**: založené na posouzení, které provádí služba Security Center, která selhala.
   - **Předaný posouzení**: seznam posouzení, které provádí služba Security Center, který předává.
   - **Nedostupná posouzení**: seznam vyhodnocení, které se nepovedlo spustit kvůli chybě nebo doporučení není relevantní pro konkrétní službu App service

   V části **doporučení** se seznam doporučení pro vybranou webovou aplikaci a závažnost jednotlivých doporučení.

   ![App Services doporučení](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Vyberte doporučení, abyste zobrazili popis doporučení a seznam špatných prostředků, zdravých prostředků a nekontrolovaných prostředků.

   - Ve sloupci **předaná vyhodnocení** je seznam předaných vyhodnocení.  Závažnost těchto hodnocení je vždy zelená.

   - Vyberte ze seznamu popis posouzení, seznam prostředků není v pořádku a v pořádku a seznam nezkontrolované prostředky předané posouzení. Je na kartě pro prostředky není v pořádku, ale tento seznam je prázdný, vždy od předaný posouzení.

     ![App Service nápravy](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů
Security Center automaticky zjišťuje, zda máte sady škálování a doporučuje nainstalovat Microsoft Monitoring Agent na tyto sady škálování. 

Instalace Microsoft Monitoring Agent: 

1. Vyberte doporučení **nainstalovat agenta monitorování do sady škálování virtuálních počítačů**. Zobrazí se seznam nemonitorované sady škálování.
2. Vyberte skupinu škálování, která není v pořádku. Postupujte podle pokynů pro instalaci agenta monitorování pomocí stávajícího naplněné pracovní plochy nebo vytvořte nový. Pokud není nastavená cenová [úroveň](security-center-pricing.md) pracovního prostoru, ujistěte se, že jste ji nastavili.

   ![Instalace MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Pokud chcete nastavit nové sady škálování tak, aby se automaticky nainstalovaly Microsoft Monitoring Agent:
1. Přejděte na Azure Policy a klikněte na **definice**.
2. Vyhledejte **nasazení zásady Log Analytics agenta pro sadu škálování virtuálních počítačů s Windows** a klikněte na něj.
3. Klikněte na **Přiřadit**.
4. Nastavte **Rozsah** a **Log Analytics pracovní prostor** a klikněte na **přiřadit**.

Pokud chcete nastavit všechny existující sady škálování pro instalaci Microsoft Monitoring Agent, v Azure Policy se můžete vrátit k nápravě a použít existující zásady na existující sady škálování.


## <a name="compute-and-app-recommendations"></a>Doporučení pro výpočetní prostředky a aplikace
|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|App Service|20|Webová aplikace by měla být přístupná jen přes HTTPS|Pouze omezit přístup k webovým aplikacím přes protokol HTTPS.|
|App Service|20|Funkce aplikace by měla být přístupná jen přes HTTPS|Přístup z aplikace Function App omezit pouze přes protokol HTTPS.|
|App Service|5|Měly by být povolené diagnostické protokoly v App Services.|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|App Service|10|Vzdálené ladění by měl být vypnuté pro webovou aplikaci|Vypněte ladění pro webové aplikace, pokud už nepotřebujete ho používat. Vzdálené ladění vyžaduje příchozí porty potřeba otevřít v aplikaci Function App.|
|App Service|10|Vzdálené ladění by měla být vypnuta aplikace – funkce|Vypněte ladění pro aplikace Function App, pokud už nepotřebujete ho používat. Vzdálené ladění vyžaduje příchozí porty potřeba otevřít v aplikaci Function App.|
|App Service|10|Nejsou povoleny všechny ("*") prostředkům pro přístup k aplikaci| Nepovolit sadu parametr WEBSITE_LOAD_CERTIFICATES "". Nastavení parametru na "znamená, že jsou načteny všechny certifikáty do úložiště osobních certifikátů webové aplikace. To může vést k porušení principu nejnižší úrovně oprávnění, jak je pravděpodobné, že web potřebuje přístup ke všem certifikátům za běhu.|
|App Service|20|CORS by nemělo umožňovat každý prostředek pro přístup k vaší webové aplikace|Povolte pouze požadované domény k interakci s vaší webovou aplikací. Pro různé prostředků zdroji (CORS) pro sdílení obsahu by nemělo umožňovat přístup k webové aplikaci všem doménám.|
|App Service|20|CORS by nemělo umožňovat každý prostředek pro přístup k aplikaci Function App| Povolte pouze požadované domény k interakci s vaší aplikací funkce. Pro různé prostředků zdroji (CORS) pro sdílení obsahu by nemělo umožňovat všechny domény pro přístup k vaší aplikaci funkcí.|
|Výpočetní prostředky (batch)|1|Pravidla upozornění na metriky by měly být nakonfigurovaná na účtech Batch.|Konfigurace pravidla upozornění na metriky na účet Batch a zapnutí metrik události dokončení odstranění fondu a spuštění události odstranění fondu|
|Výpočetní prostředky (service fabric)|10|Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.|Proveďte ověření klienta pouze prostřednictvím Azure Active Directory v Service Fabric.|
|Výpočetní prostředky (účet služby automation)|5|Proměnné účtu Automation by se měly šifrovat.|Při ukládání citlivých dat, povolte šifrování proměnných assetů účtu Automation.|
|Výpočetní prostředky (vyhledávání)|5|Auditovat povolení diagnostických protokolů pro služby vyhledávání|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (služby Service bus)|5|Měly by být povolené diagnostické protokoly v Service Bus.|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (stream analytics)|5|Měly by být povolené diagnostické protokoly v Azure Stream Analytics.|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (batch)|5|Povolení diagnostických protokolů v účtů služby Batch|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (centra událostí)|5|Měly by být povolené diagnostické protokoly v centru událostí.|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (logic apps)|5|Povolit diagnostické protokoly v Logic Apps|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (service fabric)|15|Nastavte vlastnost ClusterProtectionLevel EncryptAndSign v Service Fabric|Service Fabric nabízí tři úrovně ochrany (None, přihlašování a EncryptAndSign) pro komunikaci mezi uzly pomocí certifikátu primární clusteru.  Nastavte úroveň ochrany, k zajištění, že všechny zprávy – uzly jsou zašifrované a digitálně podepsané. |
|Výpočetní prostředky (služby Service bus)|1|Odebrat všechna autorizační pravidla s výjimkou RootManageSharedAccessKey z oboru názvů služby Service Bus |Klienti služby Service Bus neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě.|
|Výpočetní prostředky (centra událostí)|1|Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí.|Event Hub klientů neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě.|
|Výpočetní prostředky (centra událostí)|5|Měla by být definovaná autorizační pravidla pro entitu centra událostí.|Autorizační pravidla centra událostí entity udělit přístup s nejnižšími auditu.|
|Machine|50|Nainstalujte agenta Monitoring Agent na své počítače|Nainstalujte agenta monitorování povolíte shromažďování dat, aktualizace, vyhledávání směrných plánů a služby endpoint protection na každém počítači.|
|Počítač|50|Povolit automatické zřizování a shromažďování dat pro předplatná |Povolte automatické zřizování a shromažďování dat pro počítače v rámci vašich předplatných povolíte shromažďování dat, aktualizace, vyhledávání směrných plánů a služby endpoint protection na každý počítač přidat do vašich předplatných.|
|Počítač|40|Vyřešte problémy se stavem agenta Monitoring Agent na svých počítačích|Pro plnou ochranu Security Center vyřešte problémy s monitorováním agenta na počítačích, postupujte podle pokynů v příručce pro řešení potíží| 
|Počítač|40|Vyřešte problémy se stavem služby Endpoint Protection na svých počítačích|Pro plnou ochranu Security Center vyřešte problémy s monitorováním agenta na počítačích, postupujte podle pokynů v příručce pro řešení potíží.|
|Počítač|40|Vyřešte chybějící data skenování na počítačích|Řešení potíží s chybějící data skenu na virtuálních počítačích a počítačích. Chybějící data skenu na výsledky počítače ve vyhodnocování zabezpečení, jako chybějící aktualizace kontrolu směrného plánu vyhledávání a chybějící prohledávání řešení ochrany koncových bodů.|
|Počítač|40|Do vašich počítačů by se měly nainstalovat aktualizace systému|Nainstalovat chybějící zabezpečení systému a kritických aktualizací zabezpečení virtuálních počítačů s Windows a Linux a počítače
|Machine|15|Přidání brány firewall webových aplikací| Nasaďte řešení webové aplikace brány firewall (WAF) k zabezpečení webových aplikací. |
|Machine|40|Aktualizujte verzi operačního systému pro role cloudových služeb|Aktualizujte verzi operačního systému pro role cloudových služeb na nejnovější verzi, která je k dispozici pro vaši řadu operačního systému.|
|Počítač|35|Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno|Náprava ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích pro ochranu před útoky.|
|Počítač|35|Náprava ohrožení zabezpečení v konfiguraci zabezpečení pro vaše kontejnery|Pokud chcete počítače, které mají nainstalovaný Docker, chránit před útoky, napravte v konfiguraci zabezpečení ohrožení zabezpečení.|
|Počítač|25|Povolit Adaptivní řízení aplikací|Povolte aplikaci ovládacího prvku do ovládacího prvku, které aplikace můžete spouštět na vašich virtuálních počítačích v Azure. To vám pomůže posílit ochranu virtuálních počítačů před malwarem. Security Center pomocí strojového učení analyzuje aplikace spuštěné na každém virtuálním počítači a pomáhá použijete povolit pravidla těchto informací. Tato funkce zjednodušuje proces konfigurace a Správa aplikací povolit pravidla.|
|Počítač|20|Nainstalujte řešení Endpoint Protection do počítačů|Nainstalujte řešení ochrany koncových bodů na vašich virtuálních počítačů, a ochraňte je proti hrozby a ohrožení zabezpečení.|
|Počítač|20|Restartujte své počítače, aby se nainstalovaly aktualizace systému|Pokud chcete nainstalovat aktualizace systému a zabezpečit počítač před ohroženími zabezpečení, restartujte svůj počítač.|
|Machine|15|Na virtuálních počítačích by se mělo použít šifrování disku|Šifrování disků vašich virtuálních počítačů pomocí Azure Disk Encryption i pro virtuální počítače s Windows a Linux. Azure Disk Encryption (ADE) využívá oborové funkci standardní BitLocker systému Windows a funkci DM-Crypt systému Linux k poskytování šifrování disku operačního systému a dat k ochraně a chránit vaše data a vám pomohou splnit vaše organizace na zabezpečení a dodržování předpisů závazky v zákazník služby Azure key vault. Když váš požadavek na dodržování předpisů a zabezpečení vyžaduje, abyste šifrovat data začátku do konce pomocí šifrovacích klíčů, včetně šifrování dočasný disk (místně připojených dočasný) použití Azure disk encryption. Můžete také ve výchozím nastavení, spravované disky jsou šifrované v klidovém stavu ve výchozím nastavení pomocí šifrování služby úložiště Azure, kde jsou šifrovací klíče Microsoft managed keys v Azure. Pokud tím se splní vaše požadavky na zabezpečení a dodržování předpisů, můžete využít výchozí spravovaný disk encryption podle svých požadavků.|
|Machine|30|Nainstalujte řešení Posouzení ohrožení zabezpečení na virtuální počítače.|Nainstalujte řešení Posouzení ohrožení zabezpečení na virtuální počítače.|
|Počítač|15|Přidání brány firewall webových aplikací| Nasaďte řešení webové aplikace brány firewall (WAF) k zabezpečení webových aplikací. |
|Počítač|30|Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.|Virtuální počítače, u kterých je 3. stran řešení posouzení ohrožení zabezpečení nasazení průběžně hodnotí proti aplikace a ohrožení zabezpečení operačního systému. Pokaždé, když se najde takovýmto ohrožením zabezpečení, jsou dostupné pro více informací v rámci doporučení.|
|Machine|30|Nainstalujte řešení Posouzení ohrožení zabezpečení na virtuální počítače.|Nainstalujte řešení Posouzení ohrožení zabezpečení na virtuální počítače.|
|Machine|1|Virtuální počítače by měly být migrovány do nových prostředků AzureRM|Použijte Azure Resource Manager pro vaše virtuální počítače k zajištění vylepšení zabezpečení, jako je: silnější řízení přístupu (RBAC), lepší auditování, nasazení Správce prostředků a zásady správného řízení, přístup ke spravovaným identitám, přístup k trezoru klíčů pro tajné klíče, Ověřování a podpora založené na Azure AD pro značky a skupiny prostředků pro snadnější správu zabezpečení. |
|Machine|30|Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.|Virtuální počítače, u kterých je 3. stran řešení posouzení ohrožení zabezpečení nasazení průběžně hodnotí proti aplikace a ohrožení zabezpečení operačního systému. Pokaždé, když se najde takovýmto ohrožením zabezpečení, jsou dostupné pro více informací v rámci doporučení.|
|Škálovací sada virtuálních počítačů |4|Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.|Povolte protokoly a zachovejte je po dobu až do roku. To umožňuje znovu vytvořit stopy aktivity pro účely šetření. To je užitečné v případě, že dojde k incidentu zabezpečení nebo dojde k ohrožení bezpečnosti sítě.|
|Škálovací sada virtuálních počítačů|35|V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.|Opravte chyby zabezpečení v konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů, abyste je chránili před útoky. |
|Škálovací sada virtuálních počítačů|5|Napravit selhání stavu ochrany koncových bodů ve virtuálních počítačích služby Virtual Machine Scale Sets|Opravte chyby stavu ochrany koncových bodů v rámci sady škálování virtuálních počítačů, abyste je chránili před hrozbami a ohroženími zabezpečení. |
|Škálovací sada virtuálních počítačů|10|Na virtuálních počítačích by měla být nainstalovaná služba Endpoint Protection.|Nainstalujte řešení ochrany koncových bodů na vaše sady škálování virtuálních počítačů, abyste je chránili před hrozbami a ohroženími zabezpečení. |
|Škálovací sada virtuálních počítačů|40|Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.|Nainstalujte chybějící zabezpečení systému a důležité aktualizace a zabezpečte vaše služby Virtual Machine Scale Sets pro Windows a Linux. |
 





## <a name="next-steps"></a>Další postup
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:


* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.

