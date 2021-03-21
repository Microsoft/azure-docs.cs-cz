---
title: Instalace agenta Log Analytics na počítačích s Windows
description: Tento článek popisuje, jak připojit počítače s Windows hostované v jiných cloudech nebo místně, aby se Azure Monitor s agentem Log Analytics pro Windows.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: aec39b86f9651539028efce93ba6a88c3be75b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038306"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Instalace agenta Log Analytics na počítačích s Windows
Tento článek poskytuje podrobné informace o instalaci agenta Log Analytics v počítačích s Windows pomocí následujících metod:

* Ruční instalace pomocí [Průvodce instalací](#install-agent-using-setup-wizard) nebo [příkazového řádku](#install-agent-using-command-line)
* [Azure Automation konfiguraci požadovaného stavu (DSC)](#install-agent-using-dsc-in-azure-automation). 

>[!IMPORTANT]
> Metody instalace popsané v tomto článku se obvykle používají pro virtuální počítače v místním prostředí nebo v jiných cloudech. V tématu [Možnosti instalace](./log-analytics-agent.md#installation-options) získáte efektivnější možnosti, které můžete použít pro virtuální počítače Azure.

> [!NOTE]
> Pokud potřebujete nakonfigurovat agenta tak, aby vydával zprávy do více než jednoho pracovního prostoru, nemůžete to provést při počáteční instalaci, a to až po aktualizaci nastavení z ovládacích panelů nebo PowerShellu, jak je popsáno v tématu [Přidání nebo odebrání pracovního prostoru](agent-manage.md#adding-or-removing-a-workspace).  

## <a name="supported-operating-systems"></a>Podporované operační systémy

Seznam verzí Windows podporovaných agentem Log Analytics najdete v tématu [Přehled agentů Azure monitor](agents-overview.md#supported-operating-systems) .

### <a name="sha-2-code-signing-support-requirement"></a>Požadavek podpory podepisování kódu SHA-2 
Agent Windows bude začínat výhradně pomocí podepisování SHA-2 na 17. srpna 2020. Tato změna bude mít dopad na zákazníky využívající agenta Log Analytics na starší verzi operačního systému jako součást jakékoli služby Azure (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, ATP v programu Windows Defender). Tato změna nevyžaduje žádnou akci zákazníka, pokud nepoužíváte agenta na starší verzi operačního systému (Windows 7, Windows Server 2008 R2 a Windows Server 2008). Zákazníci, kteří používají starší verzi operačního systému, musí na svých počítačích provádět následující akce před 17. srpna 2020 nebo jejich agenti přestane odesílat data do svých Log Analyticsch pracovních prostorů:

1. Nainstalujte nejnovější aktualizaci Service Pack pro váš operační systém. Požadované verze service packu jsou:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Nainstalujte aktualizace pro systém Windows podepisování SHA-2 pro váš operační systém, jak je popsáno v tématu [2019 požadavky na podporu podepisování kódu pro Windows a WSUS v SHA-2](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus) .
3. Aktualizujte na nejnovější verzi agenta Windows (verze 10.20.18029).
4. Doporučuje se nakonfigurovat agenta tak, aby [používal protokol TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 

## <a name="network-requirements"></a>Požadavky sítě
Požadavky na síť pro agenta pro Windows najdete v tématu [přehled log Analyticsho agenta](./log-analytics-agent.md#network-requirements) .


   
## <a name="configure-agent-to-use-tls-12"></a>Nakonfigurovat agenta na používání protokolu TLS 1,2
Protokol [TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12) zajišťuje zabezpečení dat při přenosu pro komunikaci mezi agentem Windows a službou Log Analytics. Pokud instalujete v [operačním systému bez TLS 1,2 ve výchozím nastavení povolený](../logs/data-security.md#sending-data-securely-using-tls-12), nakonfigurujte TLS 1,2 pomocí následujících kroků.

1. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Vytvoření podklíče v části **protokoly** pro TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Vytvořte podklíč **klienta** pod podklíčem protokolu TLS 1,2 verze, který jste vytvořili dříve. Například **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**.
4. V části **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ klient** vytvořte následující hodnoty DWORD:

    * **Povoleno** [Value = 1]
    * **DisabledByDefault** [hodnota = 0]  

Nakonfigurujte .NET Framework 4,6 nebo novější, aby podporovaly zabezpečenou kryptografii, protože ve výchozím nastavení je zakázaná. [Silná kryptografie](/dotnet/framework/network-programming/tls#schusestrongcrypto) používá bezpečnější síťové protokoly jako TLS 1,2 a blokuje protokoly, které nejsou zabezpečené. 

1. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319**.  
2. V tomto podklíči vytvořte hodnotu DWORD **do schusestrongcrypto** s hodnotou **1**.  
3. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319**.  
4. V tomto podklíči vytvořte hodnotu DWORD **do schusestrongcrypto** s hodnotou **1**. 
5. Restartujte systém, aby se nastavení projevilo. 

## <a name="install-agent-using-setup-wizard"></a>Instalace agenta pomocí Průvodce instalací
Následující postup nainstaluje a nakonfiguruje agenta Log Analytics v Azure a Azure Government cloudu pomocí Průvodce instalací agenta v počítači. Pokud se chcete dozvědět, jak nakonfigurovat agenta tak, aby se také nahlásil do System Center Operations Manager skupiny pro správu, přečtěte si téma [nasazení agenta Operations Manager pomocí Průvodce instalací agenta](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. V pracovním prostoru Log Analytics klikněte na stránce **Windows servery** , na kterou jste přešli na předchozí, vyberte příslušnou verzi **agenta pro Windows** ke stažení, která se má stáhnout v závislosti na architektuře procesoru operačního systému Windows.   
2. Spusťte instalační program a nainstalujte agenta na svém počítači.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak klikněte na **Souhlasím**.
4. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak klikněte na **Další**.
5. Na stránce **Možnosti instalace agenta** zvolte připojení agenta k Azure Log Analytics a pak klikněte na **Další**.   
6. Na stránce **Azure Log Analytics** postupujte následovně:
   1. Vložte **ID pracovního prostoru** a **Klíč pracovního prostoru (primární klíč)**, které jste si zkopírovali dříve.  Pokud se má počítač hlásit do pracovního prostoru služby Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu **Cloud Azure** možnost **Azure US Government**.  
   2. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, klikněte na **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru.  Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověření proxy serveru a pak klikněte na **Další**.  
7. Jakmile dokončíte zadávání nezbytných nastavení konfigurace, klikněte na **Další**.<br><br> ![vložení ID pracovního prostoru a primárního klíče](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak klikněte na **Nainstalovat**.
9. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.

Po dokončení se **Microsoft Monitoring Agent** zobrazí v **Ovládacích panelech**. Pokud chcete potvrdit, že je hlášení Log Analytics, přečtěte si téma [ověření připojení agenta k Log Analytics](#verify-agent-connectivity-to-azure-monitor). 

## <a name="install-agent-using-command-line"></a>Instalace agenta pomocí příkazového řádku
Stažený soubor pro agenta je samostatný instalační balíček.  Instalační program pro agenta a podpůrné soubory jsou obsaženy v balíčku a je třeba jej extrahovat, aby bylo možné správně nainstalovat pomocí příkazového řádku zobrazeného v následujících příkladech.    

>[!NOTE]
>Pokud chcete upgradovat agenta, je nutné použít rozhraní API pro Log Analytics skriptování. Další informace najdete v tématu [Správa a údržba agenta Log Analytics pro systém Windows a Linux](agent-manage.md) .

V následující tabulce jsou vysvětlené konkrétní parametry podporované instalačním programem agenta, včetně nasazení pomocí Automatizace DSC.

|Možnosti specifické pro MMA                   |Poznámky         |
|---------------------------------------|--------------|
| NOAPM=1                               | Volitelný parametr. Nainstaluje agenta bez monitorování výkonu aplikace .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurace agenta pro hlášení do pracovního prostoru                |
|OPINSIGHTS_WORKSPACE_ID                | ID pracovního prostoru (GUID) pro pracovní prostor, který se má přidat                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klíč pracovního prostoru, který se používá k prvotnímu ověření v pracovním prostoru |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Zadejte cloudové prostředí, ve kterém se nachází pracovní prostor. <br> 0 = komerční cloud Azure (výchozí) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | Identifikátor URI, který se má použít pro proxy |
|OPINSIGHTS_PROXY_USERNAME               | Uživatelské jméno pro přístup k ověřenému proxy serveru |
|OPINSIGHTS_PROXY_PASSWORD               | Heslo pro přístup k ověřenému proxy serveru |

1. K extrakci instalačních souborů agenta se spustí příkazový řádek se zvýšenými oprávněními `MMASetup-<platform>.exe /c` a zobrazí výzvu k zadání cesty k extrakci souborů.  Alternativně můžete zadat cestu předáním argumentů `MMASetup-<platform>.exe /c /t:<Full Path>` .  
2. Pokud chcete nainstalovat agenta v tichém režimu a nakonfigurovat ho tak, aby se nahlásil k pracovnímu prostoru ve službě Azure Commercial Cloud, ze složky, do které jste extrahovali instalační soubory, zadejte: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   nebo chcete-li nakonfigurovat agenta tak, aby hlásil do cloudu Azure USA pro státní správu, zadejte: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Řetězcové hodnoty parametrů *OPINSIGHTS_WORKSPACE_ID* a *OPINSIGHTS_WORKSPACE_KEY* musí být zapouzdřeny do dvojitých uvozovek, aby bylo možné instalační služba systému Windows interprit jako platné možnosti pro balíček. 

## <a name="install-agent-using-dsc-in-azure-automation"></a>Instalace agenta pomocí DSC v Azure Automation

Pomocí následujícího příkladu skriptu můžete nainstalovat agenta pomocí Azure Automation DSC.   Pokud nemáte účet Automation, přečtěte si téma Začínáme [s Azure Automation](../../automation/index.yml) , abyste pochopili požadavky a kroky pro vytvoření účtu Automation, který je potřeba před použitím automatizace DSC.  Pokud nejste obeznámeni s Automatizace DSC, přečtěte si téma [Začínáme s Automatizace DSC](../../automation/automation-dsc-getting-started.md).

Následující příklad nainstaluje agenta 64, který je identifikován `URI` hodnotou. Verzi 32 můžete použít i tak, že nahradíte hodnotu identifikátoru URI. Identifikátory URI pro obě verze jsou:

- Windows 64 – bit Agent- https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 – bit Agent- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Tento postup a příklad skriptu nepodporuje upgrade agenta, který už je nasazený na počítači s Windows.

32 bitové a 64 verze balíčku agenta mají různé kódy produktů a nové verze jsou také jedinečné.  Kód produktu je identifikátor GUID, který je hlavní identifikací aplikace nebo produktu a který je reprezentován vlastností Instalační služba systému Windows **ProductCode** .  `ProductId`Hodnota ve **MMAgent.ps1m** skriptu musí odpovídat kódu produktu z balíčku 32 nebo 64 instalačního programu agenta.

Pokud chcete získat kód produktu přímo z instalačního balíčku agenta, můžete použít Orca.exe ze [součástí Windows SDK pro instalační služba systému Windows vývojářů](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) , kteří jsou součástí sady Windows Software Development Kit, nebo pomocí PowerShellu, který následuje [ukázkový skript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)  napsaný společností MVP (Microsoft hodnotný Professional).  Pro oba tyto metody musíte nejprve extrahovat soubor **MOMagent.msi** z instalačního balíčku MMASetup.  Tento postup je uveden výše v prvním kroku v části [instalace agenta pomocí příkazového řádku](#install-agent-using-command-line).  

1. Importujte modul xPSDesiredStateConfiguration DSC z nástroje [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) do Azure Automation.  
2.    Vytvoří Azure Automation variabilní prostředky pro *OPSINSIGHTS_WS_ID* a *OPSINSIGHTS_WS_KEY*. Nastavte *OPSINSIGHTS_WS_ID* na ID vašeho pracovního prostoru Log Analytics a nastavte *OPSINSIGHTS_WS_KEY* na primární klíč vašeho pracovního prostoru.
3.    Zkopírujte skript a uložte ho jako MMAgent.ps1.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Aktualizujte `ProductId` hodnotu ve skriptu pomocí kódu produktu extrahovaného z nejnovější verze instalačního balíčku agenta pomocí výše doporučených metod. 
5. [Importujte konfigurační skript MMAgent.ps1](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) do svého účtu Automation. 
6. Přiřaďte ke konfiguraci [počítač se systémem Windows nebo uzel](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) . Během 15 minut uzel zkontroluje svou konfiguraci a Agent se odešle do uzlu.

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Ověření připojení agenta k Azure Monitor

Po dokončení instalace agenta ověřte, zda je úspěšné připojení a vytváření sestav lze provést dvěma způsoby.  

V **Ovládacích panelech** na počítači vyhledejte položku **Microsoft Monitoring Agent**.  Vyberte ho a na kartě **Azure Log Analytics** by měl agent zobrazit zprávu informující o tom, **že Microsoft Monitoring Agent se úspěšně připojil ke službě Microsoft Operations Management Suite.**<br><br> ![Stav připojení MMA k Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

V Azure Portal můžete také provádět jednoduché dotazy protokolu.  

1. V Azure Portal vyhledejte a vyberte **monitor**.
1. V nabídce vyberte **protokoly** .
1. V podokně **protokoly** zadejte do pole dotazu:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Ve výsledcích hledání byste měli vidět záznamy prezenčního signálu pro počítač, který označuje, že je připojený a hlásí službu.

## <a name="cache-information"></a>Informace o mezipaměti

Data z agenta Log Analytics se ukládají do mezipaměti v místním počítači v adresáři *C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State* předtím, než se pošle do Azure monitor. Agent se pokusí o nahrání každých 20 sekund. Pokud selže, bude počkat exponenciálně rostoucí dobu, než bude úspěšná. Počká 30 sekund před druhým pokusem, 60 sekund před uplynutím následujících 120 sekund, a tak dále, a to až do maximálního počtu 8,5 hodin mezi opakovanými pokusy, dokud se znovu úspěšně nepřipojí. Tato čekací doba je mírně náhodná, aby se všechny agenti při pokusu o připojení současně nepokoušeli. Při dosažení maximální vyrovnávací paměti jsou nejstarší data zahozena.

Výchozí velikost mezipaměti je 50 MB, ale je možné ji nakonfigurovat mezi minimálně 5 MB a maximálně 1,5 GB. Je uložený v klíči registru *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum*. Hodnota představuje počet stránek s 8 KB na stránku.


## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Správa a údržba agenta Log Analytics pro systémy Windows a Linux](agent-manage.md) , kde se dozvíte, jak překonfigurovat, upgradovat nebo odebrat agenta z virtuálního počítače.

- Pokud narazíte na problémy při instalaci nebo správě agenta, přečtěte si téma [řešení potíží s agentem Windows](agent-windows-troubleshoot.md) .