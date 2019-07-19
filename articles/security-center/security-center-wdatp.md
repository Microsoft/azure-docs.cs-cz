---
title: Rozšířená ochrana před internetovými útoky v programu Windows Defender pomocí Azure Security Center
description: Tento dokument zavádí integraci mezi Azure Security Center a rozšířenou ochranou před internetovými útoky v programu Windows Defender.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: v-mohabe
ms.openlocfilehash: 87f5a14bcd6003ad81b663ed97e5349dcbff2a30
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296518"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Rozšířená ochrana před internetovými útoky v programu Windows Defender pomocí Azure Security Center

Azure Security Center rozšiřuje svoji nabídku platforem ochrany cloudových úloh na základě integrace s [rozšířenou ochranou před internetovými útoky v programu Windows Defender](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Tato změna přináší komplexní možnosti technologie EDR (Endpoint Detection and Response). Díky integraci ochrany ATP v programu Windows Defender můžete obdržíte nezvyklosti. Můžete také detekovat a reagovat na pokročilé útoky na koncových bodech serveru monitorovaných Azure Security Center.

## <a name="windows-defender-atp-features-in-security-center"></a>Funkce ochrany ATP v programu Windows Defender v Security Center

Při používání ochrany ATP v programu Windows Defender získáte:

- **Senzory pro detekci porušení zabezpečení nové generace**: Senzory ATP v programu Windows Defender pro Windows servery shromažďují obrovské množství signálů chování.

- **Zjišťování porušení narušení cloudu založeného na analýze**: Ochrana ATP v programu Windows Defender se rychle přizpůsobí měnícím se hrozbám. Používá pokročilou analýzu a velké objemy dat. Ochrana ATP v programu Windows Defender je doplněná výkonem Intelligent Security Graph s signály v systému Windows, Azure a Office pro detekci neznámých hrozeb. Poskytuje užitečné výstrahy a umožňuje rychle reagovat.

- **Analýza hrozeb**: ATP v programu Windows Defender identifikuje nástroje, techniky a postupy pro útočníky. Když je detekuje, generují výstrahy. Využívá data vygenerovaná Microsoft Threat Hunters a týmy zabezpečení a rozšiřuje je o inteligentní informace poskytované partnery.

Tyto možnosti jsou teď dostupné ve službě Azure Security Center:

- **Automatizované připojování**: Senzor ochrany ATP v programu Windows Defender je automaticky povolen pro Windows servery, které jsou připojené k Azure Security Center.

- **Jedno podokno skla**: Konzola Azure Security Center zobrazuje výstrahy ochrany ATP v programu Windows Defender.

- **Podrobné šetření počítačů**: Zákazníci Azure Security Center mají přístup ke konzole ochrany ATP v programu Windows Defender, aby provedli podrobné šetření, aby bylo možné zjistit rozsah porušení.

![Azure Security Center zobrazení seznamu výstrah a obecných informací o jednotlivých výstrahách](media/security-center-wdatp/image1.png)

Výstrahu můžete dále prozkoumat tím, že převedete do ochrany ATP v programu Windows Defender. Můžete zobrazit další informace, jako je například strom procesu výstrahy a graf incidentu. Můžete se také podívat na podrobnou časovou osu počítače, která zobrazuje každé chování po dobu až šesti měsíců.

![Stránka ATP v programu Windows Defender s podrobnými informacemi o výstraze](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Podpora platformy

Ochrana ATP v programu Windows Defender v Security Center podporuje detekci v operačních systémech Windows Server 2012 R2 a Windows Server 2016 patřících ke standardnímu předplatnému služby.

> [!NOTE]
> Při použití Azure Security Center k monitorování serverů se automaticky vytvoří tenant ATP v programu Windows Defender a data ATP v programu Windows Defender se ve výchozím nastavení uloží do Evropy. Pokud potřebujete přesunout data do jiného umístění, musíte kontaktovat podpora Microsoftu pro resetování tenanta.

## <a name="onboarding-servers-to-security-center"></a>Připojování serverů k Security Center 

Pokud chcete servery připojit k Security Center, klikněte na **Přejít na Azure Security Center a připojte servery** ze serveru Windows Defender atp.

1. V okně **připojování** vyberte nebo vytvořte pracovní prostor, do kterého chcete ukládat data. <br>
2. Pokud nevidíte všechny vaše pracovní prostory, může to být způsobeno nedostatečnými oprávněními, ujistěte se, že je váš pracovní prostor nastavený na úroveň zabezpečení Azure úrovně Standard. Další informace najdete v tématu [upgrade na úroveň Security Center úrovně Standard pro zvýšení zabezpečení](security-center-pricing.md).
    
3. Pokud chcete zobrazit pokyny k instalaci Microsoft Monitoring Agent, vyberte **Přidat servery** . 

4. Po zaregistrování můžete monitorovat počítače v části **výpočetní prostředí a aplikace**.

   ![Zprovoznění počítačů](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>Povolit integraci ochrany ATP v programu Windows Defender

Pokud chcete zobrazit, jestli je povolená integrace ochrany ATP v programu Windows Defender, vyberte**Nastavení cenové &** **Security Center** > > klikněte na své předplatné.

  ![Správa zásad Azure Security Center](media/security-center-wdatp/policy-management.png)

Tady vidíte aktuálně povolená integrace.

  ![Stránka Nastavení detekce hrozeb Azure Security Center s povolenou integrací ochrany ATP v programu Windows Defender](media/security-center-wdatp/enable-integrations.png)

- Pokud jste již servery připojili k Azure Security Center úrovně Standard, nemusíte provádět žádné další akce. Azure Security Center automaticky připojí servery do ochrany ATP v programu Windows Defender. Tato situace může trvat až 24 hodin.

- Pokud jste servery nikdy nepřipojili do Azure Security Center úrovně Standard, připojíte je k Azure Security Center běžným způsobem.

- Pokud jste servery připojili pomocí ochrany ATP v programu Windows Defender:
  - Pokyny [k odpojení serverových počítačů](https://go.microsoft.com/fwlink/p/?linkid=852906)najdete v dokumentaci.
  - Připojte tyto servery k Azure Security Center.

## <a name="access-to-the-windows-defender-atp-portal"></a>Přístup k portálu ochrany ATP v programu Windows Defender

Postupujte podle pokynů v části [přiřazení přístupu uživatele k portálu](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Nastavení konfigurace brány firewall

Pokud máte proxy nebo bránu firewall, která blokuje anonymní provoz, protože se senzor ATP v programu Windows Defender připojuje z kontextu systému, zajistěte, aby byl povolen anonymní provoz. Postupujte podle pokynů v tématu [Povolení přístupu k adresám URL služby ochrany ATP v programu Windows Defender v proxy server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testování funkce

Vygenerování neškodné výstrahy testu ochrany ATP v programu Windows Defender:

1. Pro přístup k virtuálnímu počítači s Windows Serverem 2012 R2 nebo k virtuálnímu počítači s Windows serverem 2016 použijte vzdálenou plochu.  Otevřete okno příkazového řádku.

2. Na příkazovém řádku zkopírujte a spusťte následující příkaz. Okno příkazového řádku se automaticky zavře.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Okno příkazového řádku s výše uvedeným příkazem](media/security-center-wdatp/image4.jpeg)

3. Pokud je příkaz úspěšný, zobrazí se na řídicím panelu Azure Security Center a na portálu ochrany ATP v programu Windows Defender nová výstraha. Zobrazení této výstrahy může trvat několik minut.

4. Pokud chcete zobrazit výstrahu v Security Center, přečtěte si**podezřelé příkazový řádek PowerShellu** **výstrahy** >  zabezpečení.

5. V okně šetření vyberte odkaz pro přechod na portál ochrany ATP v programu Windows Defender.

## <a name="next-steps"></a>Další postup

- [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Přečtěte si, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Přečtěte si, jak doporučení vám pomůžou chránit prostředky Azure.
- [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
