---
title: Ochrana vašich počítačů a aplikací ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje doporučení ve službě Security Center, které vám pomůžou chránit virtuální počítače a počítače a webové aplikace a služby App Service Environment.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 3a2ccd04cd7ec36cafdf56830b9ad8249f89eb7e
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321582"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Ochrana vašich počítačů a aplikací ve službě Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol. Doporučení platí pro typy prostředků Azure: virtuální počítače (VM) a počítačů, aplikací, sítí, SQL a identit a přístupů.

Tento článek se zabývá doporučení, která platí pro počítače a aplikace.

## <a name="monitoring-security-health"></a>Sledování stavu zabezpečení
Můžete monitorovat stav zabezpečení vašich prostředků na **Security Center – přehled** řídicího panelu. **Prostředky** část obsahuje počet problémů zjištěných a stavu zabezpečení pro každý typ prostředku.

Seznam všech problémů můžete zobrazit tak, že vyberete **doporučení**. Další informace o tom, jak používat doporučení, najdete v části [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

Úplný seznam výpočetní prostředky a aplikace služeb doporučení najdete v tématu [doporučení](security-center-virtual-machine-recommendations.md).

Chcete-li pokračovat, vyberte **výpočty a aplikace** pod **prostředky** nebo hlavní nabídce služby Security Center.
![Řídicí panel Security Center](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorování služby compute a aplikace
V části **výpočty a aplikace**, existují následující karty:

- **Přehled:** Monitorování a doporučení identifikovaná službou Security Center
- **Virtuální počítače a počítače:** Seznam virtuálních počítačů, počítačů a aktuální stav jejich zabezpečení
- **Cloudové služby:** Seznam webových a pracovních rolí monitorovaných službou Security Center
- **App Service (Preview)**: seznam prostředí App service a aktuální stav zabezpečení jednotlivých.
- **Kontejnery (Preview)**: seznam kontejnerů hostované na počítače s Linuxem IaaS a vyhodnocení zabezpečení jejich konfigurace Dockeru.
- **Výpočetní prostředky (Preview)**: seznam doporučení pro vaše výpočetní prostředky, jako je například clustery Service Fabric a Event hubs.

Chcete-li pokračovat, vyberte **výpočty a aplikace** pod **hygeine zabezpečení prostředků**.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

Na každé kartě můžete mít několik částí a v každé části můžete vybrat jednotlivé možnosti, abyste zobrazili další podrobnosti o doporučeném postupu k vyřešení konkrétního problému.

### Nemonitorované virtuální počítače a počítače <a name="unmonitored-vms-and-computers"></a>
Virtuální počítač nebo počítač je nemonitorovaný službou Security Center, pokud počítač není spuštěný rozšíření Microsoft Monitoring Agent. Na počítači může mít místní agent již nainstalovaný, například přímý agent OMS nebo SCOM agent. Počítače s těmito agenty se identifikují jako nemonitorované, protože tyto agenty plně nepodporuje ve službě Security Center. Pokud chcete naplno využívat všechny schopnosti služby Security Center, potřebujete rozšíření Microsoft Monitoring Agent.

Rozšíření můžete nainstalovat na nemonitorovaný virtuální počítač nebo počítač kromě již nainstalovanému místnímu agentu. Nakonfigurujte oba agenty stejným způsobem a připojte je ke stejnému pracovnímu prostoru. Tím umožníte službě Security Center pracovat s rozšířením Microsoft Monitoring Agent a shromažďovat data. Pokyny k instalaci rozšíření Microsoft Monitoring Agent najdete v tématu popisujícím [povolení rozšíření virtuálního počítače](../azure-monitor/learn/quick-collect-azurevm.md).

Další informace o důvodech, proč se službě Security Center nedaří úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování, najdete v tématu popisujícím [problémy s monitorováním stavu agenta](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Doporučení
Tato část obsahuje sadu doporučení jednotlivých virtuálních počítačů a počítačů, webových a pracovních rolí, Azure App Service Web Apps a Azure App Service Environment, která monitoruje Security Center. První sloupec uvádí doporučení. Druhý sloupec zobrazuje celkový počet prostředků, které jsou ovlivněny tímto doporučením. Třetí sloupec zobrazuje závažnost problému.

Každé doporučení obsahuje sadu akcí, které můžete provést po výběru. Pokud vyberete třeba **chybějící aktualizace systému**, počet virtuálních počítačů a počítačů s chybějícími opravami a závažnost chybějící aktualizace se zobrazí.

**Nainstalovat aktualizace systému** obsahuje souhrn důležitých aktualizací ve formátu grafu, jeden pro Windows a jeden pro Linux. Druhá část obsahuje tabulku s následujícími informacemi:

- **NÁZEV**: Název chybějící aktualizace.
- **POČET Virtuálních počítačů a počítačů**: Celkový počet virtuálních počítačů a počítačů, které chybí daná aktualizace.
- **ZÁVAŽNOST AKTUALIZACE**: Popisuje závažnost tohoto konkrétního doporučení:

    - **Kritické**: Ohrožení zabezpečení existuje u významného prostředku (aplikace, virtuálního počítače nebo skupiny zabezpečení sítě) a vyžaduje pozornost.
    - **Důležité**: Nekritická nebo další kroky jsou nutné k dokončení procesu nebo odstranění ohrožení.
    - **Střední**: Ohrožení zabezpečení, mělo by se řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)


- **STAV**: Aktuální stav doporučení:

    - **Otevřít**: Doporučení dosud nebylo řešeno.
    - **V průběhu**: Doporučení se právě zavádí k těmto prostředkům, a nevyžaduje žádné akce se.
    - **Vyřešené**: Doporučení už je dokončené. (Pokud byl problém vyřešen, položka je vyšedlá.)

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


![Virtuální počítače uvedené v pracovním prostoru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuální počítače identifikované pouze z pracovního prostoru, který je součástí zobrazeného předplatného. To zahrnuje virtuální počítače z jiných předplatných, které se hlásí k pracovnímu prostoru v tomto předplatném, a virtuální počítače nainstalované s přímým agentem SCOM, které nemají žádné ID prostředku.

Ikona, který se zobrazí pod každým doporučením, pomáhá rychle identifikovat virtuální počítač a počítač, který vyžaduje pozornost a typ doporučení se jedná. Můžete také použít filtry pro prohledání seznamu podle **typ prostředku** a **závažnost**.

Přejdete na doporučení zabezpečení pro každý virtuální počítač, klikněte na virtuální počítač.
Tady vidíte podrobné informace o zabezpečení pro virtuální počítač nebo počítač. V dolní části vidíte doporučenou akci a závažnost jednotlivých problémů.
![Cloudové služby](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Cloud Services
U cloud services doporučení vytvoří, když je aktuální verze operačního systému.

![Cloud Services](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

V případě, kdy máte doporučení (což není případ předchozího příkladu) budete muset postupovat podle kroků v doporučení a aktualizovat verzi operačního systému. Když je k dispozici aktualizace, obdržíte výstrahu (červenou nebo oranžovou – v závislosti na závažnosti problému). Když vyberete tuto výstrahu v řádku webová role 1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS) nebo WorkerRole1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS) řádků, zobrazte podrobnosti o toto doporučení.

Pokud chcete zobrazit podrobnější vysvětlení tohoto doporučení, klikněte na **Aktualizovat verzi operačního systému** ve sloupci **POPIS**.



![Aktualizace verze operačního systému](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services-preview"></a>App Services (Preview)

> [!NOTE]
> Monitorování služby App Service je ve verzi preview a je k dispozici jenom na úrovni Standard služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>
>

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

  ![Doporučení App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Vyberte na doporučení zobrazit popis doporučení a seznam prostředků není v pořádku, v dobrém stavu prostředků a nezkontrolované prostředky.

 - V části **předaný posouzení** sloupec je seznam vyhovující posouzení.  Závažnost těchto hodnocení je vždy zelená.

 -  Vyberte ze seznamu popis posouzení, seznam prostředků není v pořádku a v pořádku a seznam nezkontrolované prostředky předané posouzení. Je na kartě pro prostředky není v pořádku, ale tento seznam je prázdný, vždy od předaný posouzení.

    ![Náprava služby App Service](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)


## <a name="compute-and-app-recommendations"></a>Doporučení pro výpočetní prostředky a aplikace
|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|App Service|20|Webová aplikace by měla být přístupná jen přes HTTPS|Pouze omezit přístup k webovým aplikacím přes protokol HTTPS.|
|App Service|20|Funkce aplikace by měla být přístupná jen přes HTTPS|Přístup z aplikace Function App omezit pouze přes protokol HTTPS.|
|App Service|5|Povolení protokolování diagnostiky ve službě App service|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|App Service|10|Vzdálené ladění by měl být vypnuté pro webovou aplikaci|Vypněte ladění pro webové aplikace, pokud už nepotřebujete ho používat. Vzdálené ladění vyžaduje příchozí porty potřeba otevřít v aplikaci Function App.|
|App Service|10|Vzdálené ladění by měla být vypnuta aplikace – funkce|Vypněte ladění pro aplikace Function App, pokud už nepotřebujete ho používat. Vzdálené ladění vyžaduje příchozí porty potřeba otevřít v aplikaci Function App.|
|App Service|10|Nakonfigurovat omezení IP adres pro webovou aplikaci|Definujte seznam IP adres, které můžou přistupovat k vaší aplikace. Použití omezení IP adres, ochrání webovou aplikaci před běžnými útoky.|
|App Service|10|Nakonfigurovat omezení IP adres pro aplikaci Function App| Definujte seznam IP adres, které můžou přistupovat k vaší aplikace. Použití omezení IP adres chrání před běžnými útoky aplikaci function app.|
|App Service|10|Nejsou povoleny všechny ("*") prostředkům pro přístup k aplikaci| Nepovolit sadu parametr WEBSITE_LOAD_CERTIFICATES "". Nastavení parametru na "znamená, že jsou načteny všechny certifikáty do úložiště osobních certifikátů webové aplikace. To může vést k porušení principu nejnižší úrovně oprávnění, jak je pravděpodobné, že web potřebuje přístup ke všem certifikátům za běhu.|
|App Service|5|Webové sokety by mělo být zakázáno pro webovou aplikaci|Zkontrolujte použití webových soketů ve webových aplikacích. Protokol webových soketů se dá ohrozit různými typy bezpečnostních hrozeb.|
|App Service|5|Webové sokety by mělo být zakázáno pro aplikaci Function App|Zkontrolujte použití procedury webové sokety v rámci aplikace Function App. Protokol webových soketů se dá ohrozit různými typy bezpečnostních hrozeb.|
|App Service|5|Použijte vlastní domény pro webovou aplikaci|Použijte vlastní domény k ochraně webové aplikace před běžnými útoky, jako je útok phishing a další útoky související s DNS.|
|App Service|5|Použijte vlastní domény pro aplikaci Function App|Použijte vlastní domény k ochraně aplikace function app před běžnými útoky, jako je útok phishing a další útoky související s DNS.|
|App Service|20|CORS by nemělo umožňovat každý prostředek pro přístup k vaší webové aplikace|Povolte pouze požadované domény k interakci s vaší webovou aplikací. Pro různé prostředků zdroji (CORS) pro sdílení obsahu by nemělo umožňovat přístup k webové aplikaci všem doménám.|
|App Service|20|CORS by nemělo umožňovat každý prostředek pro přístup k aplikaci Function App| Povolte pouze požadované domény k interakci s vaší aplikací funkce. Pro různé prostředků zdroji (CORS) pro sdílení obsahu by nemělo umožňovat všechny domény pro přístup k vaší aplikaci funkcí.|
|App Service|10|Použijte nejnovější podporované rozhraní .NET Framework pro webovou aplikaci|Používejte nejnovější verzi rozhraní .NET Framework pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace.|
|App Service|10|Použít nejnovější podporovanou verzi Javy pro webovou aplikaci|Používejte nejnovější verzi Javy pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace.|
|App Service|10|Použít nejnovější podporovanou verzi PHP pro webovou aplikaci|Používejte nejnovější verzi PHP pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace.|
|App Service|10|Použít nejnovější podporovanou verzi Node.js pro webovou aplikaci|Používejte nejnovější verzi Node.js pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace.|
|App Service|10|Použít nejnovější podporovanou verzi Pythonu pro webovou aplikaci|Používejte nejnovější verzi Pythonu pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace.|
|Výpočetní prostředky (batch)|1|Konfigurace pravidla upozornění na metriky na účet Batch|Konfigurace pravidla upozornění na metriky na účet Batch a zapnutí metrik události dokončení odstranění fondu a spuštění události odstranění fondu|
|Výpočetní prostředky (service fabric)|10|Použití Azure Active Directory pro ověřování klientů v Service Fabric|Proveďte ověření klienta pouze prostřednictvím Azure Active Directory v Service Fabric.|
|Výpočetní prostředky (účet služby automation)|5| Povolení šifrování účtu Automation|Při ukládání citlivých dat, povolte šifrování proměnných assetů účtu Automation.|
|Výpočetní prostředky (nástroj pro vyrovnávání zatížení)|5|Povolení diagnostických protokolů v nástroji pro vyrovnávání zatížení|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (vyhledávání)|5|Povolení protokolování diagnostiky služby Search|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (služby Service bus)|5|Povolení protokolování diagnostiky v Service Bus|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (stream analytics)|5|Povolení protokolování diagnostiky v Azure Stream Analytics|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (service fabric)|5|Povolení protokolování diagnostiky v Service Fabric|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (batch)|5|Povolení diagnostických protokolů v účtů služby Batch|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (centra událostí)|5|Povolit diagnostické protokoly v Centru událostí|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (logic apps)|5|Povolit diagnostické protokoly v Logic Apps|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Výpočetní prostředky (service fabric)|15|Nastavte vlastnost ClusterProtectionLevel EncryptAndSign v Service Fabric|Service Fabric nabízí tři úrovně ochrany (None, přihlašování a EncryptAndSign) pro komunikaci mezi uzly pomocí certifikátu primární clusteru.  Nastavte úroveň ochrany, k zajištění, že všechny zprávy – uzly jsou zašifrované a digitálně podepsané. |
|Výpočetní prostředky (služby Service bus)|1|Odebrat všechna autorizační pravidla s výjimkou RootManageSharedAccessKey z oboru názvů služby Service Bus |Klienti služby Service Bus neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. Aby bylo v souladu s nejnižší oprávnění zabezpečení modelu, by vám mělo vytvořit zásady přístupu na úrovni entity pro fronty a témata, které poskytují přístup k jenom na konkrétní entitu.|
|Výpočetní prostředky (centra událostí)|1|Odebrat všechna autorizační pravidla s výjimkou RootManageSharedAccessKey z oboru názvů centra událostí |Event Hub klientů neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. Aby bylo v souladu s nejnižší oprávnění zabezpečení modelu, by vám mělo vytvořit zásady přístupu na úrovni entity pro fronty a témata, které poskytují přístup k jenom na konkrétní entitu.|
|Výpočetní prostředky (centra událostí)|5|Definování autorizačních pravidel na entitu centra událostí|Autorizační pravidla centra událostí entity udělit přístup s nejnižšími auditu.|
|Počítač|50|Nainstalujte agenta Monitoring Agent na své počítače|Nainstalujte agenta monitorování povolíte shromažďování dat, aktualizace, vyhledávání směrných plánů a služby endpoint protection na každém počítači.|
|Počítač|50|Povolit automatické zřizování a shromažďování dat pro předplatná |Povolte automatické zřizování a shromažďování dat pro počítače v rámci vašich předplatných povolíte shromažďování dat, aktualizace, vyhledávání směrných plánů a služby endpoint protection na každý počítač přidat do vašich předplatných.|
|Počítač|40|Vyřešte problémy se stavem agenta Monitoring Agent na svých počítačích|Pro plnou ochranu Security Center vyřešte problémy s monitorováním agenta na počítačích, postupujte podle pokynů v příručce pro řešení potíží| 
|Počítač|40|Vyřešte problémy se stavem služby Endpoint Protection na svých počítačích|Pro plnou ochranu Security Center vyřešte problémy s monitorováním agenta na počítačích, postupujte podle pokynů v příručce pro řešení potíží.|
|Počítač|40|Vyřešte chybějící data skenování na počítačích|Řešení potíží s chybějící data skenu na virtuálních počítačích a počítačích. Chybějící data skenu na výsledky počítače ve vyhodnocování zabezpečení, jako chybějící aktualizace kontrolu směrného plánu vyhledávání a chybějící prohledávání řešení ochrany koncových bodů.|
|Počítač|40|Nainstalujte aktualizace systému na své počítače|Nainstalovat chybějící zabezpečení systému a kritických aktualizací zabezpečení virtuálních počítačů s Windows a Linux a počítače
|Počítač|15|Přidání brány firewall webových aplikací| Nasaďte řešení webové aplikace brány firewall (WAF) k zabezpečení webových aplikací. |
|Počítač|40|Aktualizujte verzi operačního systému pro role cloudových služeb|Aktualizujte verzi operačního systému pro role cloudových služeb na nejnovější verzi, která je k dispozici pro vaši řadu operačního systému.|
|Počítač|35|Napravte v konfiguraci zabezpečení ohrožení zabezpečení virtuálních počítačů|Náprava ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích pro ochranu před útoky. |
|Počítač|35|Náprava ohrožení zabezpečení v konfiguraci zabezpečení pro vaše kontejnery|Pokud chcete počítače, které mají nainstalovaný Docker, chránit před útoky, napravte v konfiguraci zabezpečení ohrožení zabezpečení.|
|Počítač|25|Povolit Adaptivní řízení aplikací|Povolte aplikaci ovládacího prvku do ovládacího prvku, které aplikace můžete spouštět na vašich virtuálních počítačích v Azure. To vám pomůže posílit ochranu virtuálních počítačů před malwarem. Security Center pomocí strojového učení analyzuje aplikace spuštěné na každém virtuálním počítači a pomáhá použijete povolit pravidla těchto informací. Tato funkce zjednodušuje proces konfigurace a Správa aplikací povolit pravidla.|
|Počítač|20|Nainstalujte řešení Endpoint Protection do počítačů|Nainstalujte řešení ochrany koncových bodů na vašich virtuálních počítačů, a ochraňte je proti hrozby a ohrožení zabezpečení.|
|Počítač|20|Restartujte své počítače, aby se nainstalovaly aktualizace systému|Pokud chcete nainstalovat aktualizace systému a zabezpečit počítač před ohroženími zabezpečení, restartujte svůj počítač.|
|Počítač|15|Použít šifrování disku na virtuálních počítačích|Šifrování disků vašich virtuálních počítačů pomocí Azure Disk Encryption i pro virtuální počítače s Windows a Linux. Azure Disk Encryption (ADE) využívá oborové funkci standardní BitLocker systému Windows a funkci DM-Crypt systému Linux k poskytování šifrování disku operačního systému a dat k ochraně a chránit vaše data a vám pomohou splnit vaše organizace na zabezpečení a dodržování předpisů závazky v zákazník služby Azure key vault. Když váš požadavek na dodržování předpisů a zabezpečení vyžaduje, abyste šifrovat data začátku do konce pomocí šifrovacích klíčů, včetně šifrování dočasný disk (místně připojených dočasný) použití Azure disk encryption. Můžete také ve výchozím nastavení, spravované disky jsou šifrované v klidovém stavu ve výchozím nastavení pomocí šifrování služby úložiště Azure, kde jsou šifrovací klíče Microsoft managed keys v Azure. Pokud tím se splní vaše požadavky na zabezpečení a dodržování předpisů, můžete využít výchozí spravovaný disk encryption podle svých požadavků.|
|Počítač|30|Nainstalujte řešení Posouzení ohrožení zabezpečení na virtuální počítače.|Nainstalujte řešení Posouzení ohrožení zabezpečení na virtuální počítače.|
|Počítač|15|Přidání brány firewall webových aplikací| Nasaďte řešení webové aplikace brány firewall (WAF) k zabezpečení webových aplikací. |
|Počítač|30|Náprava ohrožení zabezpečení pomocí řešení posouzení ohrožení zabezpečení|Virtuální počítače, u kterých je 3. stran řešení posouzení ohrožení zabezpečení nasazení průběžně hodnotí proti aplikace a ohrožení zabezpečení operačního systému. Pokaždé, když se najde takovýmto ohrožením zabezpečení, jsou dostupné pro více informací v rámci doporučení.|
|Počítač|30|Nainstalujte řešení Posouzení ohrožení zabezpečení na virtuální počítače.|Nainstalujte řešení Posouzení ohrožení zabezpečení na virtuální počítače.|
|Počítač|1|Migrovat virtuální počítače do nové prostředky Azure Resource Manageru|Kvůli vylepšení zabezpečení pomocí Azure Resource Manageru pro vaše virtuální počítače: silnější access control (RBAC), lepší auditování, nasazení založené na Resource Manageru a zásad správného řízení, přístup ke spravovaným identitám přístup k trezoru klíčů pro tajné klíče, Ověřování Azure AD na základě a podpora značek a skupiny prostředků pro snadnější správu zabezpečení. |
|Počítač|30|Náprava ohrožení zabezpečení pomocí řešení posouzení ohrožení zabezpečení|Virtuální počítače, u kterých je 3. stran řešení posouzení ohrožení zabezpečení nasazení průběžně hodnotí proti aplikace a ohrožení zabezpečení operačního systému. Pokaždé, když se najde takovýmto ohrožením zabezpečení, jsou dostupné pro více informací v rámci doporučení.|

 





## <a name="next-steps"></a>Další postup
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:


* [Vysvětlení služby Azure Security Center doporučení pro virtuální počítače](security-center-virtual-machine-recommendations.md)
* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.

