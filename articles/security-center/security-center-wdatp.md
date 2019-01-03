---
title: Rozšířená ochrana před internetovými útoky pomocí Azure Security Center programu Windows Defender
description: Tento dokument přináší integraci Azure Security Center a rozšířené ochrany před internetovými útoky programu Windows Defender.
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
ms.date: 12/13/2018
ms.author: barclayn
ms.openlocfilehash: b73bf3e542801e58fbda6232eb063305785ce643
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536266"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Rozšířená ochrana před internetovými útoky pomocí Azure Security Center programu Windows Defender

Azure Security Center rozšiřuje svoji nabídku cloudových platformách ochranu úloh díky integraci s [rozšířené ochrany před internetovými útoky programu Windows Defender](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Tato změna přináší rozsáhlé možnosti koncový bod zjišťování a odpovědi (EDR). Integrace ochrany ATP v programu Windows Defender můžete vybírat anomálie. Můžete také zjistit a reagovat na pokročilých útoků na koncové body serveru monitorovaný pomocí Azure Security Center.

Zákazníci Azure Security Center teď můžou využívat funkce ochrany ATP v programu Windows Defender:

- **Generace příspěvek porušení detekci senzorů**: Ochrana ATP v programu Windows Defender senzory pro funkce Windows serverů shromažďovat velké množství chování signálů.

- **Detekce porušení zabezpečení na základě analýzy, s využitím cloudu příspěvek**: Ochrana ATP v programu Windows Defender rychle přizpůsobovat měnícím hrozbám. Využívá pokročilé analýzy a velké objemy dat. Ochrana ATP v programu Windows Defender je rozšířena výkonem Intelligent Security Graph se signály pro Windows, Azure a Office zjišťovat neznámé hrozby. Poskytuje užitečné výstrahy a umožňují rychle reagovat.

- **Hrozeb**: Ochrana ATP v programu Windows Defender identifikuje útočník nástroje, techniky a postupy. Při zjištění těchto, vygeneruje upozornění. Použije data generovaná myslivci hrozeb Microsoftu a týmu zabezpečení rozšířeno díky partnery.

Tyto možnosti jsou teď dostupné ve službě Azure Security Center:

- **Automatické připojování**: Snímač ochrany ATP v programu Windows Defender je pro servery Windows, které se připojí ke službě Azure Security Center se automaticky povolené.

- **Souhrnné zobrazení**: Konzoly Azure Security Center zobrazuje výstrahy ochrany ATP v programu Windows Defender.

- **Podrobné zkoumání počítačů**: Zákazníci Azure Security Center přístup ke konzole ochrany ATP v programu Windows Defender provádět podrobné zkoumání odhalit další obor porušení zabezpečení.

![Azure Security Center, zobrazení seznamu výstrah a obecné informace o jednotlivých výstrahách](media/security-center-wdatp/image1.png)

Je možné [upozornění prošetřit](security-center-investigation.md) ve službě Azure Security Center:

![Řídicí panel výstrah šetření ve službě Azure Security Center](media/security-center-wdatp/image2.png)

Můžete hlouběji analyzovat upozornění podle Přesun do ochrany ATP v programu Windows Defender. Zde vidíte další informace, jako je strom procesu výstrah a incidentů grafu. Můžete také zobrazit časovou osu podrobné počítač, který ukazuje každý chování historických po dobu až šest měsíců.

![Ochrana ATP v programu Windows Defender stránce s podrobnými informacemi o určité výstraze](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Podpora platformy

Tato funkce podporuje zjišťování na Windows Server 2012 R2 a Windows serveru 2016.

Jsou podporovány pouze servery v předplatných pro úrovně služeb standard.

## <a name="onboarding-servers-to-security-center"></a>Registrace serverů do služby Security Center 

Na servery připojit ke službě Security Center, klikněte na tlačítko **přejděte do Azure Security Center a připojení serverů** z registrace serveru ochrany ATP v programu Windows Defender.

1. V **připojování** okna Vybrat nebo vytvořit pracovní prostor, ve kterých se mají uložit data. <br>
2. Pokud nevidíte všechny pracovní prostory, je možné kvůli chybějícímu oprávnění, ujistěte se, že váš pracovní prostor je nastaven na úroveň Standard zabezpečení Azure. Další informace najdete v části [upgradovat na Security Center úrovně Standard pro zvýšení zabezpečení](security-center-pricing.md).
    
3.  Vyberte **přidat servery** zobrazíte pokyny o tom, jak nainstalovat agenta Microsoft Monitoring Agent. 

4.  Po připojení, můžete monitorovat počítače v rámci **výpočetní prostředky a aplikace**.

  ![Připojení počítače](media/security-center-wdatp/onboard-computers.png)


## <a name="enable-windows-defender-atp-integration"></a>Povolení integrace ochrany ATP v programu Windows Defender

Chcete-li zobrazit, pokud je povolená ochrana ATP v programu Windows Defender integrace, vyberte **Security center** > **zásady zabezpečení** > **předplatné**  >  **Upravit nastavení**.

  ![Správa zásad Azure Security Center](media/security-center-wdatp/policy-management.png)

Zde můžete zobrazit na integrace, které aktuálně povoleno.

  ![Stránka nastavení rozpoznávání hrozeb Azure Security Center s integrací ochrany ATP v programu Windows Defender povolený](media/security-center-wdatp/enable-integrations.png)

- Pokud jste už připojili servery do Azure Security Center úrovně standard, je nutné provést žádné další akce. Azure Security Center bude automaticky připojit servery do ochrany ATP v programu Windows Defender. Může to trvat až 24 hodin.

- Pokud jste nikdy připojili servery do Azure Security Center úrovně standard, připojit je ke službě Azure Security Center jako obvykle.

- Pokud jste připojili servery přes ochrany ATP v programu Windows Defender:
  - Další informace naleznete v dokumentaci pro doprovodné materiály v [jak počítače server odpojit](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Připojení ke službě Azure Security Center tyto servery.

## <a name="access-to-the-windows-defender-atp-portal"></a>Přístup k portálu ochrany ATP v programu Windows Defender

Postupujte podle pokynů v [přiřadit přístup uživatelům k portálu](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Nastavení konfigurace brány firewall

Pokud máte proxy server nebo bránu firewall, která blokuje anonymní provoz, jak senzoru ochrany ATP v programu Windows Defender se připojuje z kontextu systému, ujistěte se, že je povolený anonymní provoz. Postupujte podle pokynů v [povolit přístup k adresám URL služby ochrany ATP v programu Windows Defender v proxy serveru](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Otestovat funkci

Generovat upozornění na neškodné ochrany ATP v programu Windows Defender:

1. Použití vzdálené plochy k virtuálnímu počítači s Windows serverem 2012 R2 nebo virtuálního počítače s Windows serverem 2016.  Otevřete okno příkazového řádku.

2. Na řádku kopírovat a spusťte následující příkaz. Okno příkazového řádku se automaticky zavře.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Okno příkazového řádku pomocí výše uvedeného příkazu](media/security-center-wdatp/image4.jpeg)

3. Pokud příkaz je úspěšná, zobrazí se vám nové výstrahy na řídicím panelu Azure Security Center a na portálu ochrany ATP v programu Windows Defender. Tato výstraha může trvat několik minut.

4. Pokud chcete zkontrolovat upozornění ve službě Security Center, přejděte na **výstrahy zabezpečení** >  **podezřelý příkazový řádek Powershellu**.

5. V okně šetření vyberte odkaz přejdete na portál ochrany ATP v programu Windows Defender.

## <a name="next-steps"></a>Další postup

- [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md): Zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
- [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
