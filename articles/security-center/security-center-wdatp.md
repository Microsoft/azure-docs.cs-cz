---
title: Rozšířená ochrana před internetovými útoky v programu Microsoft Defender – Azure Security Center
description: Tento dokument představuje integraci mezi Azure Security Center a rozšířenou ochranou před internetovými útoky v programu Microsoft Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: a741fb76827327c1231890d71ee1da79e052ed50
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232406"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Rozšířená ochrana před internetovými útoky v programu Microsoft Defender pomocí Azure Security Center

Azure Security Center rozšiřuje svou cloudovou platformu ochrany, která se integruje s [pokročilou ochranou před internetovými útoky v programu Microsoft Defender](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Tato změna přináší komplexní možnosti technologie EDR (Endpoint Detection and Response). Díky integraci ochrany ATP v programu Microsoft Defender můžete obdržíte nezvyklosti. Můžete také detekovat a reagovat na pokročilé útoky na koncových bodech serveru monitorovaných Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Funkce ATP v programu Microsoft Defender v Security Center

Při používání ATP Microsoft Defenderu získáte:

- **Pokročilé senzory pro detekci narušení po porušení**: senzory ATP Microsoft Defenderu pro Windows servery shromažďují obrovské množství signálů chování.

- **Zjišťování porušení narušení cloudu založeného na analýze**: ATP v programu Microsoft Defender se rychle přizpůsobuje měnícím se hrozbám. Používá pokročilou analýzu a velké objemy dat. Ochrana ATP v programu Microsoft Defender je doplněná výkonem Intelligent Security Graph s signály v systému Windows, Azure a Office pro detekci neznámých hrozeb. Poskytuje užitečné výstrahy a umožňuje rychle reagovat.

- **Analýza hrozeb**: ATP v programu Microsoft Defender generuje výstrahy, když identifikuje nástroje, techniky a postupy pro útočníky. Využívá data vygenerovaná Microsoft Threat Hunters a týmy zabezpečení a rozšiřuje je o inteligentní informace poskytované partnery.

V Azure Security Center jsou nyní k dispozici následující možnosti:

- **Automatizované zprovoznění**: senzor ATP v programu Microsoft Defender je automaticky povolen pro Windows servery, které jsou připojené k Azure Security Center (s výjimkou těch, které používají systém windows server 2019).

- **Jediné podokno skla**: konzola Azure Security Center zobrazuje výstrahy ATP v programu Microsoft Defender.

K dalšímu zkoumání použijte ATP v programu Microsoft Defender. ATP v programu Microsoft Defender poskytuje další informace, jako je strom procesu výstrah a graf incidentu. Můžete se také podívat na podrobnou časovou osu počítače, která zobrazuje každé chování po dobu až šesti měsíců.

![Stránka ATP v programu Microsoft Defender s podrobnými informacemi o výstraze](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Podpora platformy

ATP Microsoft Defender v Security Center podporuje detekci na Windows serveru 2016, 2012 R2 a 2008 R2 SP1. pro virtuální počítače Azure potřebujete předplatné úrovně Standard a pro virtuální počítače mimo Azure budete potřebovat úroveň Standard jenom na úrovni pracovního prostoru.

> [!NOTE]
> Když použijete Azure Security Center k monitorování serverů, automaticky se vytvoří tenant Microsoft Defender ATP a data ATP programu Microsoft Defender se ve výchozím nastavení uloží do Evropy. Pokud potřebujete přesunout data do jiného umístění, musíte kontaktovat podpora Microsoftu pro resetování tenanta. Monitorování koncového bodu serveru, který využívá tuto integraci, je pro zákazníky Office 365 RSZ zakázané.

## <a name="onboarding-servers-to-security-center"></a>Připojení serverů ke službě Security Center 

Pokud chcete servery připojit k Security Center, klikněte na **Přejít na Azure Security Center a připojte servery** ze serveru Microsoft Defender atp.

1. V oblasti **připojování** vyberte nebo vytvořte pracovní prostor, do kterého chcete ukládat data. <br>
2. Pokud nevidíte všechny vaše pracovní prostory, může to být způsobeno nedostatečnými oprávněními, ujistěte se, že je váš pracovní prostor nastavený na úroveň zabezpečení Azure úrovně Standard. Další informace najdete v tématu [upgrade na úroveň Security Center úrovně Standard pro zvýšení zabezpečení](security-center-pricing.md).
    
3. Pokud chcete zobrazit pokyny k instalaci agenta Log Analytics, vyberte **Přidat servery** . 

4. Po zaregistrování můžete monitorovat počítače v části **výpočetní prostředí a aplikace**.

   ![Zprovoznění počítačů](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Povolit integraci ATP v programu Microsoft Defender

Pokud chcete zobrazit, jestli je povolená integrace služby Microsoft Defender ATP, vyberte**Nastavení cenové &** **Security Center** > > klikněte na své předplatné.
Tady vidíte aktuálně povolená integrace.

  ![Stránka Nastavení detekce hrozeb Azure Security Center s povolenou integrací ATP v programu Microsoft Defender](media/security-center-wdatp/enable-integrations.png)

- Pokud jste již servery připojili k Azure Security Center úrovně Standard, nemusíte provádět žádné další akce. Azure Security Center automaticky připojí servery do ochrany ATP v programu Microsoft Defender. Připojování může trvat až 24 hodin.

- Pokud jste servery nikdy nepřipojili do Azure Security Center úrovně Standard, připojíte je k Azure Security Center běžným způsobem.

- Pokud jste servery připojili pomocí ochrany ATP v programu Microsoft Defender:
  - Pokyny [k odpojení serverových počítačů](https://go.microsoft.com/fwlink/p/?linkid=852906)najdete v dokumentaci.
  - Připojte tyto servery k Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Přístup k portálu Microsoft Defender ATP

Postupujte podle pokynů v části [přiřazení přístupu uživatele k portálu](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Nastavení konfigurace brány firewall

Pokud máte proxy nebo bránu firewall, která blokuje anonymní provoz, protože se senzor ATP Microsoft Defender připojuje z kontextu systému, zajistěte, aby byl povolen anonymní provoz. Postupujte podle pokynů v tématu [Povolení přístupu k adresám URL služby Microsoft Defender ATP v proxy server](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testování funkce

Vygenerování neškodné výstrahy testu ATP v programu Microsoft Defender:

1. Vytvořte složku ' C:\test-MDATP-test '.

1. Pro přístup k virtuálnímu počítači s Windows Serverem 2012 R2 nebo k virtuálnímu počítači s Windows serverem 2016 použijte vzdálenou plochu. Otevřete okno příkazového řádku.

1. Na příkazovém řádku zkopírujte a spusťte následující příkaz. Okno příkazového řádku se automaticky zavře.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Okno příkazového řádku s výše uvedeným příkazem](media/security-center-wdatp/image4.jpeg)

1. Pokud je příkaz úspěšný, zobrazí se na řídicím panelu Azure Security Center a na portálu Microsoft Defender ATP nové upozornění. Zobrazení této výstrahy může trvat několik minut.

1. Pokud chcete zobrazit výstrahu v Security Center, přečtěte si > **podezřelé příkazový řádek PowerShellu** **výstrahy zabezpečení**.

1. V okně šetření vyberte odkaz pro přechod na portál Microsoft Defender ATP.

## <a name="next-steps"></a>Další kroky

- [Platformy a funkce podporované službou Azure Security Center](security-center-os-coverage.md)
- [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
- [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.