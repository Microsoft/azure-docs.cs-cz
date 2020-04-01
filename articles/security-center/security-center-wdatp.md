---
title: Pokročilá ochrana před internetovými hrozbami v programu Microsoft Defender – Azure Security Center
description: Tento dokument představuje integraci mezi Azure Security Center a Microsoft Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 5136a00421aebaa72998b1dfcdf75feb935851c6
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435439"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Pokročilá ochrana před hrozbami s Azure Security Center

Azure Security Center rozšiřuje svou nabídku cloudových platforem ochrany úloh integrací s [pokročilým ochranou před internetovými hrozbami (ATP) programu Microsoft Defender.](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp)
Tato změna přináší komplexní možnosti technologie EDR (Endpoint Detection and Response). Díky integraci ochrany ATP v programu Microsoft Defender můžete rozpoznat abnormality. Můžete také zjistit a reagovat na pokročilé útoky na koncové body serveru monitorované službou Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Funkce ochrany ATP v programu Microsoft Defender v centru zabezpečení

Při použití programu Microsoft Defender ATP získáte následující:

- **Pokročilé senzory detekce po porušení zabezpečení**: Senzory ATP programu Microsoft Defender pro servery se systémem Windows shromažďují širokou škálu behaviorálních signálů.

- **Detekce porušení zabezpečení po cloudu založená na analytice**: Ochrana ATP v programu Microsoft Defender se rychle přizpůsobí měnícím se hrozbám. Využívá pokročilé analýzy a big data. Ochrana ATP v programu Microsoft Defender je umocněna výkonem inteligentního grafu zabezpečení se signály napříč Windows, Azure a Office pro detekci neznámých hrozeb. Poskytuje upozornění, která lze žalovat, a umožňuje rychlou reakci.

- **Threat Intelligence**: Microsoft Defender ATP generuje výstrahy, když identifikuje útočníka nástroje, techniky a postupy. Využívá data generovaná lovci hrozeb společnosti Microsoft a bezpečnostními týmy, rozšířená o inteligenci poskytovanou partnery.

V Azure Security Center jsou teď dostupné následující možnosti:

- **Automatické registrace**: Snímač OCHRANY ATP programu Microsoft Defender je automaticky povolen pro servery se systémem Windows, které jsou na palubě centra zabezpečení Azure.

- **Jediné skleněné podokno**: Konzola Azure Security Center zobrazuje výstrahy ochrany ATP v programu Microsoft Defender.

- **Podrobné šetření počítače**: Zákazníci služby Azure Security Center mohou pomocí konzoly OCHRANY ATP v programu Microsoft Defender provést podrobné šetření, aby odhalili rozsah porušení zabezpečení.

![Azure Security Center, zobrazení seznamu výstrah a obecné informace o každé výstrahy](media/security-center-wdatp/image1.png)

Chcete-li dále prozkoumat, použijte microsoft defender atp. Ochrana ATP v programu Microsoft Defender poskytuje další informace, jako je například strom procesu výstrahy a graf incidentů. Můžete také zobrazit podrobnou časovou osu počítače, která zobrazuje každé chování za historické období až šest měsíců.

![Stránka Ochrany ATP v programu Microsoft Defender s podrobnými informacemi o výstraze](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Podpora platformy

Ochrana ATP v programu Microsoft Defender v centru zabezpečení podporuje zjišťování na Windows Server 2016, 2012 R2 a 2008 R2 SP1, pro virtuální počítače Azure potřebujete předplatné úrovně Standard a pro virtuální počítače jiného než Azure potřebujete úroveň Standard pouze na úrovni pracovního prostoru.

> [!NOTE]
> Při použití Azure Security Center ke sledování serverů, Microsoft Defender ATP tenanta se automaticky vytvoří a Microsoft Defender ATP data jsou uložena v Evropě ve výchozím nastavení. Pokud potřebujete přesunout data do jiného umístění, musíte kontaktovat podporu Microsoftu a resetovat klienta. Monitorování koncových bodů serveru využívající tuto integraci bylo pro zákazníky Office 365 GCC zakázáno.

## <a name="onboarding-servers-to-security-center"></a>Připojení serverů ke službě Security Center 

Pokud chcete přejít na servery do Centra zabezpečení, klikněte na **Přejít na Azure Security Center na napalubé servery** z registrace serveru Microsoft Defender ATP.

1. V oblasti **Registrace** vyberte nebo vytvořte pracovní prostor, do kterého chcete data uložit. <br>
2. Pokud nevidíte všechny pracovní prostory, může to být kvůli nedostatku oprávnění, ujistěte se, že váš pracovní prostor je nastavený na úroveň Azure Security Standard. Další informace naleznete v [tématu Upgrade na úroveň Standard centra zabezpečení, kde najdete rozšířené zabezpečení](security-center-pricing.md).
    
3. Výběrem **možnosti Přidat servery** zobrazíte pokyny k instalaci agenta Analýzy protokolů. 

4. Po zapnutí můžete monitorovat počítače v části **Výpočetní výkon a aplikace**.

   ![Palubní počítače](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Povolení integrace ochrany ATP v programu Microsoft Defender

Chcete-li zobrazit, zda je povolena integrace ochrany ATP v programu Microsoft Defender, vyberte možnost > **Nastavení cenového &** **centra zabezpečení**> kliknutí na předplatné.
Zde můžete vidět aktuálně povolené integrace.

  ![Stránka nastavení detekce hrozeb Centra zabezpečení Azure s povolenou integrací ochrany ATP v programu Microsoft Defender](media/security-center-wdatp/enable-integrations.png)

- Pokud jste už zadali servery na standardní úroveň Azure Security Center, nemusíte provádět žádné další akce. Azure Security Center bude automaticky zapalubě servery Microsoft Defender ATP. Registrace může trvat až 24 hodin.

- Pokud jste servery nikdy nezaložili na standardní úrovni Azure Security Center, založte je do Azure Security Center jako obvykle.

- Pokud jste servery zadali prostřednictvím programu Microsoft Defender ATP:
  - Pokyny k [tomu, jak serverové počítače offboardovat,](https://go.microsoft.com/fwlink/p/?linkid=852906)naleznete v dokumentaci.
  - Zavěste tyto servery do Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Přístup k portálu ochrany ATP programu Microsoft Defender

Postupujte podle pokynů v [části Přiřadit uživateli přístup k portálu](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Nastavení konfigurace brány firewall

Pokud máte proxy server nebo bránu firewall, která blokuje anonymní provoz, jako microsoft defender atp senzor se připojuje z kontextu systému, ujistěte se, že anonymní provoz je povolen. Postupujte podle pokynů v části [Povolit přístup k adresám URL služby Microsoft Defender ATP na serveru proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testování funkce

Chcete-li vygenerovat neškodnou výstrahu testu ochrany ATP programu Microsoft Defender:

1. Vytvořte složku C:\test-MDATP-test.

1. Pomocí vzdálené plochy můžete přistupovat k virtuálnímu počítači Se systémem Windows Server 2012 R2 nebo k virtuálnímu počítači se systémem Windows Server 2016. Otevřete okno příkazového řádku.

1. Na příkazu prompt zkopírujte a spusťte následující příkaz. Okno příkazového řádku se automaticky zavře.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Okno příkazového řádku s výše uvedeným příkazem](media/security-center-wdatp/image4.jpeg)

3. Pokud je příkaz úspěšný, zobrazí se nová výstraha na řídicím panelu Azure Security Center a na portálu ochrany ATP v programu Microsoft Defender. Zobrazení tohoto upozornění může trvat několik minut.

4. Chcete-li zkontrolovat výstrahu v Centru zabezpečení, přejděte na **příkaz Zabezpečení Podezřelé** > **příkazové řádky prostředí PowerShell**.

5. V okně šetření vyberte odkaz pro přejděte na portál ochrany ATP programu Microsoft Defender.

## <a name="next-steps"></a>Další kroky

- [Platformy a funkce podporované službou Azure Security Center](security-center-os-coverage.md)
- [Nastavení zásad zabezpečení v Azure Security Center:](tutorial-security-policy.md)Zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení v Azure Security Center:](security-center-recommendations.md)Zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
- [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
