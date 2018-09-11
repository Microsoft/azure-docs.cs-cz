---
title: Windows Defender Advanced Threat Protection (ATP) pomocí služby Azure Security Center (Public preview) | Dokumentace Microsoftu
description: Tento dokument přináší integraci Azure Security Center a ochrana ATP v programu Windows Defender.
services: security-center
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2018
ms.author: barclayn
ms.openlocfilehash: f2cb4edd469d76f79e4134ca261bac5263bf3ce4
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296053"
---
# <a name="windows-defender-advanced-threat-protection-atp-with-azure-security-center-public-preview"></a>Windows Defender Advanced Threat Protection (ATP) pomocí služby Azure Security Center (Public preview)

Azure Security Center rozšiřuje svoji nabídku cloudové úlohy ochrany platformy (CWPP) díky integraci s [ochrany ATP v programu Windows Defender](https://www.microsoft.com/WindowsForBusiness/windows-atp).
Tato změna přináší rozsáhlé možnosti koncový bod zjišťování a odpovědi (EDR). Umožňuje přímé anomálie, detekovat a reagovat na pokročilých útoků na koncové body serveru služba ASC monitorovat.

Zákazníci Azure Security Center teď můžou využívat funkce Windows Defender ATP:

- **Příspěvek Next gen porušení zabezpečení detekci senzorů:** senzor ochrany ATP v programu Windows Defender pro Windows servery, která shromažďuje rozsáhlé pole chování signály, povolte detekci pokročilých útoků.

- **Detekce porušení zabezpečení na základě analýzy, s využitím cloudu příspěvek:** ochrany ATP v programu Windows Defender rychle přizpůsobovat měnícím hrozbám. Zaměstnává pokročilé analýzy a velké objemy dat. Je rozšířena výkonem Intelligent Security Graph se signály pro Windows, Azure a Office zjišťovat neznámé hrozby. Poskytuje užitečné výstrahy a umožňují rychle reagovat.

- **Hrozeb**: identifikuje útočník nástroje, techniky a postupy ochrany ATP v programu Windows Defender a generuje výstrahy, když jsou dodržovány tyto. Využívá data vygeneruje myslivci Microsoft teams zabezpečení a rozšířen o analýzy hrozeb, které jsou k dispozici od partnerů.

Tyto možnosti jsou teď dostupné ve službě Azure Security Center:

- Windows servery, které se připojí k ASC se automaticky zapnulo automatické připojování – senzor ochrany ATP v programu Windows Defender

- Souhrnné zobrazení v ASC - nejsou k dispozici v konzole ASC výstrahy Windows Defender ATP

- Podrobné zkoumání machine - ASC zákazníci přístup ke konzole ochrany ATP v programu Windows Defender provádět podrobné zkoumání odhalit další obor porušení zabezpečení

![* Obrázek 1 zobrazuje obrázek celé při zkoumání včetně výstrah generovaných ASC *](media/security-center-wdatp/image1.png)

Můžete prozkoumat upozornění ve službě Azure Security Center:

![Obrázek 2 šetření - Azure Security Center](media/security-center-wdatp/image2.png)

Můžete hlouběji analyzovat upozornění podle Přesun do ochrany ATP v programu Windows Defender. Zde můžete zobrazit další informace jako strom procesu výstrah, incidentu grafu a časové osy podrobné počítače všechny chování vystavení historických po dobu až šest měsíců.

![Obrázek 3 šetření – ochrana ATP v programu Windows Defender](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Podpora platformy

Tato funkce podporuje zjišťování na Windows Server 2012 R2 a Windows serveru 2016.

Pouze servery v předplatných na úrovni Standard

## <a name="onboarding-instructions"></a>Instrukce k Onboardingu

- Pokud jste už připojili požadované servery na úroveň standard ASC - nic ASC se připojení automaticky serverů WDATP.

- Pokud jste nikdy připojení serverů k ASC standardní úroveň – připojení k ASC jako obvykle.

- Pokud jste připojili servery přes WDATP:
  - Další informace naleznete v dokumentaci pro doprovodné materiály v [jak počítače server odpojit](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Připojení k ASC

## <a name="access-to-wdatp-portal"></a>Přístup k portálu WDATP

Postupujte podle pokynů [přiřadit přístup uživatelům k portálu](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)

## <a name="set-firewall-configuration"></a>Konfigurace nastavení brány firewall

Pokud máte proxy server nebo bránu firewall, která blokuje anonymní provoz, jak senzor ochrany ATP v programu Windows Defender se připojuje z kontextu systému, ujistěte se, že je povolené anonymní provoz; postupujte podle pokynů [povolit přístup k adresám URL služby ochrany ATP v programu Windows Defender v proxy serveru](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server)

## <a name="how-can-i-test-the-feature"></a>Jak můžu otestovat funkci?

 Takto bude generovat neškodné výstraha WDATP testu.

1. Připojte se přes RDP jeden z virtuálních počítačů Windows serveru (2012 R2 nebo 2016) v předplatném a otevřete okno příkazového řádku

2. Na řádku kopírovat a spusťte následující příkaz. Všimněte si, že se automaticky zavře okno příkazového řádku.

    -ExecutionPolicy - NoExit **PowerShell.exe obejít - WindowStyle skrytý (System.Net.WebClient New-Object). StazenySoubor ("http://127.0.0.1/1.exe', ' C:\\testovací WDATP\\invoice.exe'); Spustit proces ' C:\\testovací WDATP\\invoice.exe. **

  ![Obrázek příkazu výše v okně příkazového řádku](media/security-center-wdatp/image4.jpeg)

3. V případě úspěchu se nová výstraha se zobrazí za pár minut ASC a ochrana ATP v programu WD portálu.

4. Přezkoumejte toto upozornění ve službě Azure Security Center, přejděte na **upozornění zabezpečení –\> podezřelý příkazový řádek prostředí Powershell**

5. Z šetření okna klikněte na odkaz pro přesměrování na portál WDATP

## <a name="next-steps"></a>Další postup

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
