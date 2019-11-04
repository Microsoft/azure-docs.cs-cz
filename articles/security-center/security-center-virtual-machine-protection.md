---
title: Ochrana počítačů a aplikací v Azure Security Center | Microsoft Docs
description: Tento dokument popisuje doporučení v Security Center, která vám pomůžou chránit vaše virtuální počítače a počítače a vaše webové aplikace a App Service prostředí.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 2317d0206e3fc4a342606d17c172ac42cbe82870
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520676"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Ochrana počítačů a aplikací v Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure, serverů mimo Azure a virtuálních počítačů. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků. Doporučení se vztahují na typy prostředků Azure: virtuální počítače a počítače, aplikace, sítě, SQL a identita a přístup.

Tento článek se zabývá doporučeními, která se vztahují na počítače a aplikace.

## <a name="monitoring-security-health"></a>Sledování stavu zabezpečení
Stav zabezpečení vašich prostředků můžete monitorovat na řídicím panelu **Security Center – přehled** . Část **Resources (prostředky** ) poskytuje počet zjištěných problémů a stav zabezpečení pro každý typ prostředku.

Seznam všech problémů můžete zobrazit výběrem **doporučení**. Další informace o tom, jak používat doporučení, najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

Úplný seznam doporučení COMPUTE a App Services najdete v tématu [věnovaném doporučením](security-center-virtual-machine-protection.md#compute-and-app-recs).

Pokud chcete pokračovat, vyberte **compute & aplikace** v části **prostředky** nebo v hlavní nabídce Security Center.
řídicí panel Security Center ![](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorování výpočetních a aplikačních služeb
V části **výpočetní & aplikace**jsou k dispozici následující karty:

- **Přehled:** Monitorování a doporučení identifikovaná službou Security Center
- **Virtuální počítače a počítače:** Seznam virtuálních počítačů, počítačů a aktuální stav jejich zabezpečení
- **Cloudové služby:** Seznam webových a pracovních rolí monitorovaných službou Security Center
- **App Services**: seznam prostředí App Service a aktuální stav zabezpečení každého.
- **Kontejnery**: Seznam kontejnerů a posouzení zabezpečení jejich konfigurací.
- **Výpočetní prostředky (Preview)** : seznam doporučení pro výpočetní prostředky, například Service Fabric clusterů a centra událostí.

Pokud chcete pokračovat, vyberte **compute & aplikace** v části **hygiena zabezpečení prostředků**.

![Služby Compute](./media/security-center-virtual-machine-recommendations/compute.png)

Na každé kartě můžete mít několik částí a v každé části můžete vybrat jednotlivé možnosti, abyste zobrazili další podrobnosti o doporučeném postupu k vyřešení konkrétního problému.

### Nemonitorované virtuální počítače a počítače<a name="unmonitored-vms-and-computers"></a>
VIRTUÁLNÍ počítač nebo počítač se nemonitoruje Security Center, pokud počítač nepoužívá rozšíření Microsoft Monitoring Agent. Počítač může mít již nainstalovaný místní agent, například agenta OMS Direct nebo Agent System Center Operations Manager. Počítače s těmito agenty jsou označeny jako nemonitorované, protože tito agenti nejsou v Security Center plně podporovaná. Pokud chcete naplno využívat všechny schopnosti služby Security Center, potřebujete rozšíření Microsoft Monitoring Agent.

Kromě již nainstalovaného místního agenta můžete nainstalovat rozšíření na nemonitorovaný virtuální počítač nebo počítač. Nakonfigurujte oba agenty stejným způsobem a připojte je ke stejnému pracovnímu prostoru. Tím umožníte službě Security Center pracovat s rozšířením Microsoft Monitoring Agent a shromažďovat data. Pokyny k instalaci rozšíření Microsoft Monitoring Agent najdete v tématu popisujícím [povolení rozšíření virtuálního počítače](../azure-monitor/learn/quick-collect-azurevm.md).

Další informace o důvodech, proč se službě Security Center nedaří úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování, najdete v tématu popisujícím [problémy s monitorováním stavu agenta](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Doporučení
Tato část obsahuje sadu doporučení pro každý virtuální počítač a počítač, webové a pracovní role, Azure App Service Web Apps a Azure App Service Environment, které Security Center monitorují. První sloupec uvádí doporučení. Druhý sloupec zobrazuje celkový počet prostředků, které jsou ovlivněny tímto doporučením. Třetí sloupec zobrazuje závažnost problému.

Každé doporučení obsahuje sadu akcí, které můžete provést po jeho výběru. Pokud například vyberete **chybějící aktualizace systému**, zobrazí se počet virtuálních počítačů a počítačů, ve kterých chybí opravy, a závažnost chybějící aktualizace.

**Použití aktualizací systému** obsahuje souhrn důležitých aktualizací ve formátu grafu, jeden pro Windows a jeden pro Linux. Druhá část obsahuje tabulku s následujícími informacemi:

- **NÁZEV:** Název chybějící aktualizace.
- **Ne. Virtuálních počítačů &ch počítačů**: celkový počet virtuálních počítačů a počítačů, ve kterých chybí Tato aktualizace.
- **Závažnost aktualizace**: popisuje závažnost tohoto konkrétního doporučení:

    - **Kritické**: ohrožení zabezpečení existuje smysluplným prostředkem (aplikace, virtuální počítač nebo skupina zabezpečení sítě) a vyžaduje pozornost.
    - **Důležité**: k dokončení procesu nebo odstranění ohrožení zabezpečení se vyžadují nekritické nebo další kroky.
    - **Střední**: ohrožení zabezpečení by se mělo řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)


- **STAV**: Aktuální stav doporučení:

    - **Otevřené**: Doporučení dosud nebylo řešeno.
    - **Probíhá**: Doporučení se aktuálně na tyto prostředky používá a není třeba provádět žádnou akci.
    - **Vyřešeno**: Doporučení už je dokončené. (Pokud byl problém vyřešen, položka je vyšedlá.)

Pokud chcete zobrazit podrobnosti o doporučení, klikněte na název chybějící aktualizace v seznamu.


> [!NOTE]
> Bezpečnostní doporučení uvedená tady jsou stejná jako na dlaždici **doporučení** . Další informace o řešení doporučení najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Virtuální počítače a počítače
Část virtuální počítače a počítače nabízí přehled všech doporučení pro virtuální počítače a počítače. Každý sloupec představuje jednu sadu doporučení.

![Doporučení pro virtuální počítač a počítač](./media/security-center-virtual-machine-recommendations/vm-computers.png)

V tomto seznamu jsou zastoupeny čtyři typy ikon:

![Počítač umístěný mimo Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Počítač mimo Azure.

![Azure Resource Manager virtuálního počítače](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager virtuální počítač.

![Virtuální počítač Azure Classic](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Virtuální počítač Azure Classic.


![Virtuální počítače identifikované z pracovního prostoru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuální počítače identifikované pouze z pracovního prostoru, který je součástí zobrazeného předplatného. To zahrnuje virtuální počítače z jiných předplatných, které jsou součástí pracovního prostoru v tomto předplatném, a virtuální počítače, které byly nainstalovány s Operations Manager přímým agentem, a nemají žádné ID prostředku.

Ikona, která se zobrazí pod každým doporučením, vám pomůže rychle identifikovat virtuální počítač a počítač, který vyžaduje pozornost, a typ doporučení. Pomocí filtrů můžete také Hledat v seznamu podle **typu prostředku** a podle **závažnosti**.

Pokud chcete přejít k podrobnostem o zabezpečení pro každý virtuální počítač, klikněte na virtuální počítač.
Tady vidíte podrobnosti zabezpečení pro virtuální počítač nebo počítač. V dolní části vidíte doporučenou akci a závažnost každého problému.
![Cloudové služby](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Cloudové služby
Pro cloudové služby se vytvoří doporučení, když je verze operačního systému zastaralá.

![Cloudové služby](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

V případě, kdy máte doporučení (což není případ předchozího příkladu), musíte postupovat podle kroků v doporučení a aktualizovat verzi operačního systému. Když je k dispozici aktualizace, obdržíte výstrahu (červenou nebo oranžovou – v závislosti na závažnosti problému). Když vyberete tuto výstrahu v WebRole1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS) nebo WorkerRole1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS), zobrazí se další podrobnosti o tomto doporučení.

Pokud chcete zobrazit podrobnější vysvětlení tohoto doporučení, klikněte na **Aktualizovat verzi operačního systému** ve sloupci **POPIS**.



![Aktualizace verze operačního systému](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Aplikační služby
Pokud chcete zobrazit informace o App Service, musíte ve svém předplatném povolit App Service. Pokyny k povolení této funkce najdete v tématu [ochrana App Service pomocí Azure Security Center](security-center-app-services.md).
[!NOTE]
> App Service monitorování je ve verzi Preview a je k dispozici pouze na úrovni Standard Security Center.


V části **App Services**najdete seznam prostředí App Service Environment a shrnutí stavu na základě Security Center prováděného hodnocení.

![Aplikační služby](./media/security-center-virtual-machine-recommendations/app-services.png)

V tomto seznamu jsou zastoupeny tři typy ikon:

![Prostředí App Services](./media/security-center-virtual-machine-recommendations/ase.png) Prostředí App Services.

![Webová aplikace](./media/security-center-virtual-machine-recommendations/web-app.png) Webová aplikace

![Aplikace Function](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikace Function.

1. Vyberte webovou aplikaci. Otevře se souhrnné zobrazení se třemi kartami:

   - **Doporučení**: na základě posouzení provedených Security Center, která selhala.
   - **Úspěšná vyhodnocení**: seznam hodnocení provedených Security Center, která byla úspěšná.
   - **Nedostupná posouzení**: seznam posouzení, která se nepodařilo spustit z důvodu chyby, nebo doporučení není relevantní pro konkrétní službu App Service.

   V části **doporučení** je seznam doporučení pro vybranou webovou aplikaci a závažnost každého doporučení.

   ![App Services doporučení](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Vyberte doporučení, abyste zobrazili popis doporučení a seznam špatných prostředků, zdravých prostředků a nekontrolovaných prostředků.

   - Ve sloupci **předaná vyhodnocení** je seznam předaných vyhodnocení.  Závažnost těchto hodnocení je vždycky zelená.

   - V seznamu vyberte úspěšné posouzení, seznam stavů, které jsou v pořádku, a v seznamu nekontrolovaných prostředků. Pro prostředky, které nejsou v pořádku, je k dispozici karta, ale tento seznam je vždy prázdný, protože hodnocení bylo úspěšné.

     ![App Service nápravy](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů
Security Center automaticky zjišťuje, zda máte sady škálování a doporučuje nainstalovat Microsoft Monitoring Agent na tyto sady škálování. 

Instalace Microsoft Monitoring Agent: 

1. Vyberte doporučení **nainstalovat agenta monitorování do sady škálování virtuálních počítačů**. Zobrazí se seznam nemonitorované sady škálování.
2. Vyberte skupinu škálování, která není v pořádku. Postupujte podle pokynů pro instalaci agenta monitorování pomocí stávajícího naplněné pracovní plochy nebo vytvořte nový. Pokud není nastavená [cenová úroveň](security-center-pricing.md) pracovního prostoru, ujistěte se, že jste ji nastavili.

   ![Instalace MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Pokud chcete nastavit nové sady škálování tak, aby se automaticky nainstalovaly Microsoft Monitoring Agent:
1. Přejděte na Azure Policy a klikněte na **definice**.
2. Vyhledejte **nasazení zásady Log Analytics agenta pro sadu škálování virtuálních počítačů s Windows** a klikněte na něj.
3. Klikněte na **Přiřadit**.
4. Nastavte **Rozsah** a **Log Analytics pracovní prostor** a klikněte na **přiřadit**.

Pokud chcete nastavit všechny existující sady škálování pro instalaci Microsoft Monitoring Agent, v Azure Policy se můžete vrátit k **nápravě** a použít existující zásady na existující sady škálování.


## Doporučení pro výpočty a aplikace<a name="compute-and-app-recs"></a>
|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|App Service|20|Webová aplikace by měla být přístupná jen přes protokol HTTPS|Omezte přístup k webovým aplikacím jenom přes HTTPS.|
|App Service|20|Function App by měl být přístupný jenom přes HTTPS|Omezte přístup aplikací Function App jenom přes protokol HTTPS.|
|App Service|5|Měly by být povolené diagnostické protokoly v App Services.|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|App Service|10|Vzdálené ladění by mělo být pro webovou aplikaci vypnuté.|Vypnout ladění pro webové aplikace, pokud už je nepotřebujete používat. Vzdálené ladění vyžaduje, aby byly na Function App otevřené porty pro příchozí spojení.|
|App Service|10|Vzdálené ladění by mělo být pro aplikaci funkcí vypnuté.|Vypněte ladění pro Function App, pokud už je nepotřebujete používat. Vzdálené ladění vyžaduje, aby byly na Function App otevřené porty pro příchozí spojení.|
|App Service|10|Nepovolit všem (' * ') prostředkům přístup k aplikaci| Nepovolujte u parametru WEBSITE_LOAD_CERTIFICATES hodnotu "". Nastavením parametru na ' ' znamená, že všechny certifikáty budou načteny do osobního úložiště certifikátů vaší webové aplikace. To může vést k zneužití principu nejnižší úrovně oprávnění, protože je pravděpodobné, že lokalita potřebuje mít přístup ke všem certifikátům za běhu.|
|App Service|20|CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím|Povoluje interakci jenom požadovaných domén s webovou aplikací. Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat přístup k vaší webové aplikaci všem doménám.|
|App Service|20|CORS by neměl umožňovat každému prostředku přístup k vašemu Function App| Povoluje interakci jenom požadovaných domén s vaší aplikací funkcí. Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat všem doménám přístup k vaší aplikaci funkcí.|
|Výpočetní prostředky (Batch)|1\. místo|Pravidla upozornění na metriky by měly být nakonfigurovaná na účtech Batch.|Nakonfigurujte pravidla upozornění metrik na účtu Batch a povolte události odstranit kompletní události a odstranit fond. události spuštění odstranění fondu|
|Výpočetní prostředky (Service Fabric)|10|Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.|Ověřování klienta provádějte pouze prostřednictvím Azure Active Directory v Service Fabric.|
|Výpočetní prostředky (účet Automation)|5|Proměnné účtu Automation by se měly šifrovat.|Povoluje šifrování prostředků proměnných účtu služby Automation při ukládání citlivých dat.|
|Výpočetní prostředky (hledání)|5|Auditovat povolení diagnostických protokolů pro služby vyhledávání|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Service Bus)|5|Měly by být povolené diagnostické protokoly v Service Bus.|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Stream Analytics)|5|Měly by být povolené diagnostické protokoly v Azure Stream Analytics.|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Batch)|5|Povolení diagnostických protokolů v účtech Batch|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (centrum událostí)|5|Měly by být povolené diagnostické protokoly v centru událostí.|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Logic Apps)|5|Povolit diagnostické protokoly v Logic Apps|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Service Fabric)|15|Nastavte vlastnost ClusterProtectionLevel na EncryptAndSign v Service Fabric|Service Fabric poskytuje tři úrovně ochrany (žádné, podpisové a EncryptAndSign) pro komunikaci mezi uzly pomocí primárního certifikátu clusteru.  Nastavte úroveň ochrany tak, aby bylo zajištěno, že všechny zprávy mezi uzly budou šifrovány a digitálně podepsány. |
|Výpočetní prostředky (Service Bus)|1\. místo|Odebrat všechna autorizační pravidla s výjimkou RootManageSharedAccessKey z oboru názvů Service Bus |Klienti Service Bus by neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě.|
|Výpočetní prostředky (centrum událostí)|1\. místo|Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí.|Klienti centra událostí by neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě.|
|Výpočetní prostředky (centrum událostí)|5|Měla by být definovaná autorizační pravidla pro entitu centra událostí.|Auditujte autorizační pravidla v entitě centra událostí, abyste udělili přístup s minimálním oprávněním.|
|Počítač|50|Instalace agenta monitorování do počítačů|Nainstalujte agenta monitorování, aby bylo možné na každém počítači povolit shromažďování dat, kontrolu aktualizací, kontrolu základních hodnot a službu Endpoint Protection.|
|Počítač|50|Povolení automatického zřizování a shromažďování dat pro vaše předplatná |Povolením automatického zřizování a shromažďování dat pro počítače ve vašich předplatných umožníte shromažďování dat, kontrolu aktualizací, kontrolu standardních hodnot a službu Endpoint Protection na každém počítači přidávaném do vašich předplatných.|
|Počítač|40|Řešení problémů se stavem agenta monitorování na vašich počítačích|Pokud chcete úplnou Security Center ochranu, vyřešte problémy s agentem monitorování na vašich počítačích podle pokynů v Průvodci odstraňováním potíží.| 
|Počítač|40|Řešení problémů se stavem služby Endpoint Protection na vašich počítačích|V případě úplné ochrany Security Center vyřešte problémy s agentem monitorování na vašich počítačích podle pokynů v Průvodci odstraňováním potíží.|
|Počítač|40|Řešení potíží s chybějícími daty kontroly na vašich počítačích|Řešení potíží s chybějícími daty kontroly na virtuálních počítačích a počítačích Chybějící data kontroly na vašich počítačích mají za následek chybějící vyhodnocení zabezpečení, jako je například skenování aktualizací, kontrola standardních hodnot a chybějící kontrola řešení ochrany koncových bodů.|
|Počítač|40|Do vašich počítačů by se měly nainstalovat aktualizace systému|Pro zabezpečení virtuálních počítačů a počítačů se systémem Windows a Linux nainstalujte chybějící zabezpečení systému a důležité aktualizace.
|Počítač|15|Přidání brány firewall webových aplikací| Nasazením řešení Firewall webových aplikací (WAF) zabezpečíte své webové aplikace. |
|Počítač|40|Aktualizace verze operačního systému pro vaše role cloudové služby|Aktualizujte verzi operačního systému (OS) pro role cloudové služby na nejnovější verzi dostupnou pro vaši rodinu operačních systémů.|
|Počítač|35|Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno|Opravte chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích, abyste je chránili před útoky.|
|Počítač|35|Oprava ohrožení zabezpečení v konfiguraci zabezpečení v kontejnerech|Napravit ohrožení zabezpečení v konfiguraci zabezpečení na počítačích s nainstalovaným Docker pro ochranu před útoky|
|Počítač|25|Povolit adaptivní řízení aplikací|Povolením řízení aplikací můžete řídit, které aplikace se můžou spouštět na virtuálních počítačích umístěných v Azure. To vám pomůže posílit vaše virtuální počítače proti malwaru. Security Center využívá Machine Learning k analýze aplikací spuštěných na každém virtuálním počítači a pomůže vám použít pravidla pro povolení pomocí těchto inteligentních funkcí. Tato možnost zjednodušuje proces konfigurace a správy pravidel povolených aplikací.|
|Počítač|20|Instalace řešení Endpoint Protection na počítačích|Nainstalujte řešení ochrany koncových bodů na virtuální počítače, abyste je chránili před hrozbami a ohroženími zabezpečení.|
|Počítač|20|Restartujte počítače, aby se projevily systémové aktualizace.|Restartujte počítače, aby se projevily aktualizace systému, a zabezpečte počítač před ohrožením zabezpečení.|
|Počítač|15|Na virtuálních počítačích by se mělo použít šifrování disku|Zašifrujte disky virtuálních počítačů pomocí Azure Disk Encryption pro virtuální počítače s Windows i Linux. Azure Disk Encryption (ADE) využívá standardní funkci nástroje BitLocker systému Windows a funkci DM-crypt systému Linux k poskytnutí šifrování operačního systému a datového disku pro zajištění ochrany a ochrany vašich dat a lepšího zabezpečení a dodržování předpisů vaší organizace. závazky v trezoru klíčů zákazníka Azure V případě, že požadavek na dodržování předpisů a zabezpečení vyžaduje, abyste zašifrují data zakončení pomocí šifrovacích klíčů, včetně šifrování dočasného (místně připojeného dočasného) disku, použijte Azure Disk Encryption. Ve výchozím nastavení se Managed Disks standardně šifrují v klidovém stavu pomocí Azure Storage šifrování služby, kde šifrovací klíče jsou spravované klíče Microsoftu v Azure. Pokud to vyhovuje vašim požadavkům na dodržování předpisů a zabezpečení, můžete využít výchozí šifrování spravovaného disku, které splňuje vaše požadavky.|
|Počítač|30|Instalace řešení pro posouzení ohrožení zabezpečení na virtuálních počítačích|Instalace řešení pro posouzení ohrožení zabezpečení na virtuálních počítačích|
|Počítač|15|Přidání brány firewall webových aplikací| Nasazením řešení Firewall webových aplikací (WAF) zabezpečíte své webové aplikace. |
|Počítač|30|Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.|Virtuální počítače, pro které je nasazené řešení zabezpečení od jiných výrobců, se průběžně vyhodnocuje proti chybám zabezpečení aplikací a operačních systémů. Pokaždé, když jsou taková ohrožení zabezpečení zjištěná, jsou k dispozici pro další informace jako součást doporučení.|
|Počítač|30|Instalace řešení pro posouzení ohrožení zabezpečení na virtuálních počítačích|Instalace řešení pro posouzení ohrožení zabezpečení na virtuálních počítačích|
|Počítač|1\. místo|Virtuální počítače by měly být migrovány do nových prostředků AzureRM|Použijte Azure Resource Manager pro vaše virtuální počítače k zajištění vylepšení zabezpečení, jako je: silnější řízení přístupu (RBAC), lepší auditování, nasazení Správce prostředků a zásady správného řízení, přístup ke spravovaným identitám, přístup k trezoru klíčů pro tajné klíče, Ověřování a podpora založené na Azure AD pro značky a skupiny prostředků pro snadnější správu zabezpečení. |
|Počítač|30|Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.|Virtuální počítače, pro které je nasazené řešení zabezpečení od jiných výrobců, se průběžně vyhodnocuje proti chybám zabezpečení aplikací a operačních systémů. Pokaždé, když jsou taková ohrožení zabezpečení zjištěná, jsou k dispozici pro další informace jako součást doporučení.|
|Škálovací sada virtuálních počítačů |4|Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.|Povolte protokoly a zachovejte je po dobu až do roku. To umožňuje znovu vytvořit stopy aktivity pro účely šetření. To je užitečné v případě, že dojde k incidentu zabezpečení nebo dojde k ohrožení bezpečnosti sítě.|
|Škálovací sada virtuálních počítačů|35|V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.|Opravte chyby zabezpečení v konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů, abyste je chránili před útoky. |
|Škálovací sada virtuálních počítačů|5|Napravit selhání stavu ochrany koncových bodů ve virtuálních počítačích služby Virtual Machine Scale Sets|Opravte chyby stavu ochrany koncových bodů v rámci sady škálování virtuálních počítačů, abyste je chránili před hrozbami a ohroženími zabezpečení. |
|Škálovací sada virtuálních počítačů|10|Na virtuálních počítačích by měla být nainstalovaná služba Endpoint Protection.|Nainstalujte řešení ochrany koncových bodů na vaše sady škálování virtuálních počítačů, abyste je chránili před hrozbami a ohroženími zabezpečení. |
|Škálovací sada virtuálních počítačů|40|Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.|Nainstalujte chybějící zabezpečení systému a důležité aktualizace a zabezpečte vaše služby Virtual Machine Scale Sets pro Windows a Linux. |
 





## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících tématech:


* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Monitorování identity a přístupu ve službě Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL Service v Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.

