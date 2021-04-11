---
title: Používání programu Microsoft Defender pro licenci koncového bodu, který je součástí Azure Security Center
description: Přečtěte si o programu Microsoft Defender pro koncové body a nasaďte ho z Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/08/2021
ms.author: memildin
ms.openlocfilehash: 88d0a3dcd89ea678d77bc558fc680630bc0f2309
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168174"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Ochrana koncových bodů pomocí integrovaného řešení EDR Security Center: Microsoft Defender pro koncové body

Microsoft Defender pro koncové body je holistický, cloudové řešení zabezpečení koncového bodu. Hlavní funkce jsou:

- Řízení a hodnocení rizik na základě rizika 
- Omezení možností útoku
- Ochrana založená na chování a cloudové ochrany
- Detekce a odpověď koncového bodu (EDR)
- Automatické šetření a náprava
- Spravované lovecké služby

> [!TIP]
> Byl původně spuštěn jako ochrana **ATP v programu Windows Defender**, produkt pro detekci a odezvu koncového bodu (EDR) byl přejmenován v 2019 jako **ATP v programu Microsoft Defender**.
>
> V Ignite 2020 jsme spustili [sadu Microsoft Defender XDR](https://www.microsoft.com/security/business/threat-protection) a tato komponenta EDR se přejmenovala na **Microsoft Defender pro koncový bod**.


## <a name="availability"></a>Dostupnost

| Aspekt                          | Podrobnosti                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stav vydaných verzí:                  | Všeobecně dostupná (GA)                                                                                                                                                                                                                                                                                      |
| Stanov                        | Vyžaduje [Azure Defender pro servery](defender-for-servers-introduction.md) .                                                                                                                                                                                                                                             |
| Podporované platformy:            |  • Počítače Azure s Windows<br> • Počítače s obloukem v Azure, na kterých běží Windows|
| Podporované verze systému Windows:  |  • **Obecná dostupnost (GA) –** detekce na Windows serveru 2016, 2012 R2 a 2008 R2 SP1<br> • Zjišťování **verze Preview** na windows serveru 2019, [Windows Virtual desktopu (WVD)](../virtual-desktop/overview.md)a [Windows 10 Enterprise multi-session](../virtual-desktop/windows-10-multisession-faq.yml) (dřív Enterprise pro virtuální plochy (evd)|
| Nepodporované operační systémy:  |  • Windows 10 (jiné než EVD nebo WVD)<br> • Linux|
| Požadované role a oprávnění: | Povolení nebo zakázání integrace: **Správce zabezpečení** nebo **vlastník**<br>Zobrazení upozornění MDATP v Security Center: **čtecí modul zabezpečení**, **Čtenář**, **Přispěvatel skupiny prostředků**, **vlastník skupiny prostředků**, **Správce zabezpečení**, **vlastník předplatného** nebo **přispěvatele předplatného**|
| Cloud                         | ![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Microsoft Defender pro funkce koncového bodu v Security Center

Microsoft Defender pro koncové body poskytuje:

- **Pokročilé senzory detekce po porušení**. Defender pro senzory koncového bodu pro počítače s Windows shromažďují obrovské množství signálů chování.

- **Analýza, která je založená na analýze, na základě cloudu a odhalení po porušení**. Defender pro koncové body se rychle přizpůsobí měnícím se hrozbám. Používá pokročilou analýzu a velké objemy dat. Je vylepšená výkonem Intelligent Security Graph a signály v systému Windows, Azure a Office ke zjištění neznámých hrozeb. Poskytuje užitečné výstrahy a umožňuje rychle reagovat.

- **Analýza hrozeb:** Defender pro koncový bod generuje výstrahy, když identifikuje nástroje, techniky a postupy pro útočníka. Využívá data vygenerovaná Microsoft Threat Hunters a týmy zabezpečení a rozšiřuje je o inteligentní informace poskytované partnery.

Integrací programu Defender pro koncový bod s Security Center se vám bude vytěžit z následujících dalších možností:

- **Automatizované připojování**. Security Center automaticky povolí Microsoft Defender pro koncové body pro všechny servery Windows monitorované pomocí Security Center.

- **Jedno podokno skla** Konzola Security Center zobrazí výstrahu programu Microsoft Defender pro koncové body. Pro další zkoumání použijte Microsoft Defender pro vlastní stránky portálu, kde se zobrazí další informace, jako je strom procesu výstrahy a graf incidentu. Můžete se také podívat na podrobnou časovou osu počítače, která zobrazuje každé chování po dobu až šesti měsíců.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender pro vlastní Security Center koncového bodu" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender pro umístění tenanta koncového bodu

Když použijete Azure Security Center k monitorování serverů, automaticky se vytvoří Microsoft Defender pro klienta koncového bodu. Data shromážděná programem Defender pro koncový bod jsou uložená v geograficky umístění tenanta, jak je uvedeno během zřizování. Zákaznická data – v pseudonymně naformátované formě se můžou ukládat do centrálního úložného a zpracovatelského systému v USA. 

Po nakonfigurování umístění ho nemůžete změnit. Pokud máte vlastní licenci pro Microsoft Defender pro koncový bod a potřebujete přesunout vaše data do jiného umístění, obraťte se na podpora Microsoftu, aby tenant obnovil.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Povolení integrace programu Microsoft Defender pro koncové body

1. Ověřte, že váš počítač splňuje požadavky nezbytné pro Defender pro koncový bod:

    - Pro **všechny verze systému Windows**:
        - Konfigurace nastavení sítě popsaná v tématu [Konfigurace nastavení proxy zařízení a připojení k Internetu](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
        - Pokud nasazujete Defender na koncový bod do místních počítačů, připojte ho ke službě Azure ARC, jak je vysvětleno v tématu [připojení hybridních počítačů se servery s podporou ARC Azure](../azure-arc/servers/learn/quick-enable-hybrid-vm.md) .
    - Pro **počítače s Windows serverem 2019** se navíc ujistěte, že běží na platném agentovi a že mají rozšíření MicrosoftMonitoringAgent.

1. Povolte **Azure Defender pro servery**. Další informace najdete v tématu [rychlý Start: povolení programu Azure Defender](enable-azure-defender.md).

1. Pokud jste už na svých serverech licencovaný a nasazený program Microsoft Defender pro koncové body, odeberte ho pomocí postupu popsaného v tématu [odpojení Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers).
1. V nabídce Security Center vyberte **cenové & nastavení**.
1. Vyberte předplatné, které chcete změnit.
1. Vyberte **detekci hrozeb**.
1. Zaškrtněte políčko **povolí programu Microsoft Defender, aby měl koncový bod přístup k datům**, a vyberte **Uložit**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Povolení integrace mezi Azure Security Center a řešením Microsoftu EDR a Microsoft Defender pro koncový bod":::

    Azure Security Center automaticky zařadí vaše servery do programu Microsoft Defender pro koncový bod. Připojování může trvat až 24 hodin.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Přístup k programu Microsoft Defender pro portál koncových bodů

1. Ujistěte se, že uživatelský účet má potřebná oprávnění. Další informace najdete v [přiřazení přístupu uživatele k Security Center programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Ověřte, jestli máte proxy nebo bránu firewall, která blokuje anonymní provoz. Defender pro senzor koncového bodu se připojuje z kontextu systému, takže musí být povolený anonymní provoz. Pokud chcete zajistit nerušený přístup k programu Defender pro portál Endpoint, postupujte podle pokynů v tématu [Povolení přístupu k adresám URL služby v proxy server](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Otevřete [portál Microsoft Defender Security Center](https://securitycenter.windows.com/). Přečtěte si další informace o funkcích a ikonách portálu v článku [Přehled portálu Microsoft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Odeslat testovací výstrahu

Pro vygenerování neškodné výstrahy testování v programu Microsoft Defender pro koncové body:

1. Vytvořte složku ' C:\test-MDATP-test '.
1. Pro přístup k počítači použijte vzdálenou plochu.
1. Otevřete okno příkazového řádku.
1. Na příkazovém řádku zkopírujte a spusťte následující příkaz. Okno příkazového řádku se automaticky zavře.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Okno příkazového řádku s příkazem pro vygenerování testovací výstrahy.":::

1. Pokud je příkaz úspěšný, zobrazí se nová výstraha na řídicím panelu Azure Security Center a v programu Microsoft Defender pro portál koncového bodu. Zobrazení této výstrahy může trvat několik minut.
1. Pokud chcete zobrazit výstrahu v Security Center, přečtěte si   >  **podezřelé příkazový řádek PowerShellu** výstrahy zabezpečení.
1. V okně šetření vyberte odkaz pro přechod na portál Microsoft Defender pro koncový bod.

    > [!TIP]
    > Výstraha se aktivuje s **informační** závažností.

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Nejčastější dotazy k integrovanému programu Microsoft Defender pro Security Center pro koncové body

- [Jaké jsou licenční požadavky pro Microsoft Defender pro koncový bod?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Pokud již mám licenci pro program Microsoft Defender pro koncový bod, mohu získat slevu na Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Návody přepnout z nástroje EDR třetí strany?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Jaké jsou licenční požadavky pro Microsoft Defender pro koncový bod?
Defender pro koncový bod je v **Azure Defenderu pro servery** zahrnutý bez dalších poplatků. Alternativně se dá koupit samostatně pro 50 počítače a další.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Pokud již mám licenci pro program Microsoft Defender pro koncový bod, mohu získat slevu na Azure Defender?
Pokud jste už získali licenci na Microsoft Defender pro koncový bod, nebudete muset platit za tuto část vaší licence na Azure Defender.

Pokud chcete potvrdit svoji slevu, obraťte se na tým podpory Security Center a poskytněte příslušnému ID pracovního prostoru relevantní informace o licenci, oblasti a licence pro jednotlivé příslušné licence.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Návody přepnout z nástroje EDR třetí strany?
Úplné pokyny pro přepínání z řešení koncového bodu od jiného výrobce než od Microsoftu jsou dostupné v dokumentaci k Microsoft Defenderu pro koncové body: [Přehled migrace](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Další kroky

- [Platformy a funkce podporované službou Azure Security Center](security-center-os-coverage.md)
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.