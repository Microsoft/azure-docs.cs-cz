---
title: Rozšířená ochrana před internetovými útoky v programu Microsoft Defender – Azure Security Center
description: Tento dokument představuje integraci mezi Azure Security Center a rozšířenou ochranou před internetovými útoky v programu Microsoft Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: memildin
ms.openlocfilehash: 92feb159fe4c893a55d37fa90c34acf4c4c93631
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826177"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Rozšířená ochrana před internetovými útoky v programu Microsoft Defender pomocí Azure Security Center

Azure Security Center se integruje s [pokročilou ochranou před internetovými útoky v programu Microsoft Defender (ATP)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) , aby poskytovala komplexní možnosti detekce a odezvy koncových bodů


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Vyžaduje [Azure Defender](security-center-pricing.md) .|
|Podporované počítače:|![Ano](./media/icons/yes-icon.png) Počítače Azure s Windows<br>![Ano](./media/icons/yes-icon.png) Počítače ARC Azure s Windows|
|Požadované role a oprávnění:|Povolení nebo zakázání integrace: **Správce zabezpečení** nebo **vlastník**<br>Zobrazení upozornění MDATP v Security Center: **čtecí modul zabezpečení**, **Čtenář**, **Přispěvatel skupiny prostředků**, **vlastník skupiny prostředků**, **Správce zabezpečení**, **vlastník předplatného**nebo **přispěvatele předplatného**|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy.<br>![No](./media/icons/no-icon.png) Zákazníci v RSZ, kteří mají spuštěné úlohy ve veřejných cloudech Azure<br>![Ano](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>Funkce ATP v programu Microsoft Defender v Security Center

ATP v programu Microsoft Defender poskytuje:

- **Pokročilé senzory pro detekci narušení po porušení**: senzory ATP Microsoft Defenderu pro Windows servery shromažďují obrovské množství signálů chování.

- **Zjišťování porušení narušení cloudu založeného na analýze**: ATP v programu Microsoft Defender se rychle přizpůsobuje měnícím se hrozbám. Používá pokročilou analýzu a velké objemy dat. Ochrana ATP v programu Microsoft Defender je doplněná výkonem Intelligent Security Graph s signály v systému Windows, Azure a Office pro detekci neznámých hrozeb. Poskytuje užitečné výstrahy a umožňuje rychle reagovat.

- **Analýza hrozeb**: ATP v programu Microsoft Defender generuje výstrahy, když identifikuje nástroje, techniky a postupy pro útočníky. Využívá data vygenerovaná Microsoft Threat Hunters a týmy zabezpečení a rozšiřuje je o inteligentní informace poskytované partnery.


Integrací služby Defender ATP s Azure Security Center můžete také využít výhod následujících dalších možností:

- **Automatizovaná**registrace: Integration automaticky povoluje senzor ATP v programu Microsoft Defender pro servery Windows, které monitorují Azure Security Center (pokud nepoužívají systém Windows Server 2019).

- **Jediné podokno skla**: konzola Azure Security Center zobrazuje výstrahy ATP v programu Microsoft Defender. K dalšímu zkoumání použijte ATP v programu Microsoft Defender. ATP v programu Microsoft Defender poskytuje další informace, jako je strom procesu výstrah a graf incidentu. Můžete se také podívat na podrobnou časovou osu počítače, která zobrazuje každé chování po dobu až šesti měsíců.

    ![Stránka ATP v programu Microsoft Defender s podrobnými informacemi o výstraze](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Podpora platformy

ATP Microsoft Defender v Security Center podporuje detekci v systému Windows Server 2016, 2012 R2 a 2008 R2 SP1. Pro virtuální počítače Azure, které potřebujete k povolení Azure Defenderu na vašem předplatném a pro virtuální počítače mimo Azure, potřebujete, aby byl v pracovním prostoru jenom povolený Azure Defender.

Monitorování koncového bodu serveru pomocí této integrace bylo pro zákazníky Office 365 RSZ zakázané.

## <a name="data-storage-location"></a>Umístění úložiště dat

Když použijete Azure Security Center k monitorování serverů, vytvoří se automaticky tenant Microsoft Defender ATP. Data shromažďovaná ATP programu Microsoft Defender se ukládají do geografického umístění tenanta, jak je uvedeno během zřizování. Zákaznická data v nepseudonymních formulářích můžou být uložená také v centrálním systému úložiště a zpracování v USA. 

Po nakonfigurování nebudete moct změnit umístění, kde jsou data uložená. Pokud potřebujete přesunout data do jiného umístění, kontaktujte podpora Microsoftu pro resetování tenanta.


## <a name="onboard-servers-to-security-center"></a>Připojit servery k Security Center 

Pokud chcete servery připojit k Security Center, klikněte na **Přejít na Azure Security Center a připojte servery** ze serveru Microsoft Defender atp.

1. V oblasti **připojování** vyberte nebo vytvořte pracovní prostor, do kterého chcete ukládat data.

2. Pokud nevidíte všechny vaše pracovní prostory, může to být způsobeno nedostatečnými oprávněními, ujistěte se, že je váš pracovní prostor chráněný pomocí Azure Defenderu.
    
3. Pokud chcete zobrazit pokyny k instalaci agenta Log Analytics, vyberte **Přidat servery** . 

4. Po registraci můžete počítače monitorovat v [inventáři assetů](asset-inventory.md).

   ![Zprovoznění počítačů](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Povolit integraci ATP v programu Microsoft Defender

Pokud chcete zobrazit, jestli je povolená integrace **ochrany**ATP v programu Microsoft Defender, vyberte  >  **Nastavení cenové &** Security Center > vyberte předplatné.

Tady vidíte aktuálně povolená integrace.

  ![Stránka Nastavení detekce hrozeb Azure Security Center s povolenou integrací ATP v programu Microsoft Defender](media/security-center-wdatp/enable-integrations.png)

- Pokud jste už Azure Defender povolili, nemusíte dělat nic dalšího. Azure Security Center automaticky připojí servery do ochrany ATP v programu Microsoft Defender. Připojování může trvat až 24 hodin.

- Pokud jste servery nikdy nepřipojili k Azure Security Center, připojte je k Azure Security Center a umožněte jako obvykle službu Azure Defender.

- Pokud jste servery připojili pomocí ochrany ATP v programu Microsoft Defender:
  - Pokyny [k odpojení serverových počítačů](https://go.microsoft.com/fwlink/p/?linkid=852906)najdete v dokumentaci.
  - Připojte tyto servery k Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Přístup k portálu Microsoft Defender ATP

1. Postupujte podle pokynů v části [přiřazení přístupu uživatele k portálu](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Ověřte, jestli máte proxy nebo bránu firewall, která blokuje anonymní provoz. Senzor ATP (Defender ATP) se připojuje z kontextu systému, takže musí být povolený anonymní provoz. Pokud chcete zajistit nerušený přístup k portálu Microsoft Defender ATP, postupujte podle pokynů v tématu [Povolení přístupu k adresám URL služby Microsoft Defender ATP v proxy server](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

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

1. Pokud chcete zobrazit výstrahu v Security Center, přečtěte si **Security alerts**  >  **podezřelé příkazový řádek PowerShellu**výstrahy zabezpečení.

1. V okně šetření vyberte odkaz pro přechod na portál Microsoft Defender ATP.

## <a name="next-steps"></a>Další kroky

- [Platformy a funkce podporované službou Azure Security Center](security-center-os-coverage.md)
- [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
- [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.