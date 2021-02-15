---
title: Vytvoření místního prostředí Integration Runtime
description: Naučte se, jak vytvořit místní prostředí Integration runtime v Azure Data Factory, které umožňuje přístup k datovým továrnám v privátní síti.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.date: 02/10/2021
ms.openlocfilehash: 3e61b6a0f17d2d21aaaebc5ff42b0221cf851a4b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389488"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Vytvoření a konfigurace místního prostředí Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Prostředí Integration runtime (IR) je výpočetní infrastruktura, kterou Azure Data Factory používá k poskytování funkcí pro integraci dat napříč různými síťovými prostředími. Podrobnosti o IR najdete v tématu [Přehled prostředí Integration runtime](concepts-integration-runtime.md).

Místní prostředí Integration runtime může spouštět aktivity kopírování mezi cloudovým úložištěm dat a úložištěm dat v privátní síti. Můžou také odesílat transformační aktivity s výpočetními prostředky v místní síti nebo ve službě Azure Virtual Network. Instalace místního prostředí Integration runtime potřebuje místní počítač nebo virtuální počítač v privátní síti.  

Tento článek popisuje, jak můžete vytvořit a nakonfigurovat prostředí IR v místním prostředí.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="considerations-for-using-a-self-hosted-ir"></a>Pokyny k používání prostředí IR v místním prostředí

- Můžete použít jeden místní prostředí Integration runtime pro několik místních zdrojů dat. Můžete ji také sdílet s jinou datovou továrnou v rámci stejného tenanta Azure Active Directory (Azure AD). Další informace najdete v tématu [sdílení prostředí Integration runtime](./create-shared-self-hosted-integration-runtime-powershell.md)v místním prostředí.
- Do libovolného počítače můžete nainstalovat jenom jednu instanci prostředí Integration runtime v místním prostředí. Pokud máte dva datové továrny, které potřebují přístup k místním zdrojům dat, pomocí [funkce pro sdílení IR](./create-shared-self-hosted-integration-runtime-powershell.md) v místním prostředí můžete sdílet místně hostovaný modul IR nebo nainstalovat technologii IR v místním prostředí do dvou místních počítačů, jednu pro každou datovou továrnu.  
- Místní prostředí Integration runtime nemusí být ve stejném počítači jako zdroj dat. Nicméně když se v místním prostředí Integration runtime blíží ke zdroji dat, zkracuje se čas, který prostředí Integration runtime v místním prostředí umožňuje připojit se ke zdroji dat. Doporučujeme nainstalovat modul runtime integrace v místním prostředí do počítače, který se liší od druhého, který je hostitelem místního zdroje dat. Pokud je místní prostředí Integration runtime a zdroj dat na různých počítačích, Integration runtime v místním prostředí nesoutěží se zdrojem dat pro prostředky.
- Můžete mít několik místních prostředí Integration runtime na různých počítačích, které se připojují ke stejnému místnímu zdroji dat. Například pokud máte dva místně hostované prostředí Integration runtime, které obsluhují dva datové továrny, může být stejný místní zdroj dat zaregistrován pomocí obou datových továrn.
- Využijte místní prostředí Integration runtime k podpoře integrace dat v rámci virtuální sítě Azure.
- Považovat zdroj dat za místní zdroj dat, který je za bránou firewall, a to i v případě, že používáte Azure ExpressRoute. Pomocí místního prostředí Integration runtime připojte službu ke zdroji dat.
- Využijte místní prostředí Integration runtime i v případě, že je úložiště dat v cloudu na virtuálním počítači infrastruktury Azure jako služba (IaaS).
- Úlohy můžou selhat v místním prostředí Integration runtime, které jste nainstalovali na Windows Server, pro který je povolené šifrování kompatibilní se standardem FIPS. Pokud chcete tento problém obejít, máte dvě možnosti: uložení přihlašovacích údajů/tajných hodnot v Azure Key Vault nebo zakázání šifrování kompatibilního se standardem FIPS na serveru. Chcete-li zakázat šifrování kompatibilní se standardem FIPS, změňte hodnotu v následujícím podklíči registru z 1 (povoleno) na 0 (zakázáno): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled` . Pokud použijete místní [prostředí Integration runtime jako proxy pro SSIS Integration runtime](./self-hosted-integration-runtime-proxy-ssis.md), může být povoleno šifrování kompatibilní se standardem FIPS a bude použito při přesunu dat z místního prostředí do Azure Blob Storage jako pracovní oblast.

## <a name="command-flow-and-data-flow"></a>Tok příkazů a tok dat

Když přesouváte data mezi místními a cloudem, aktivita používá místní prostředí Integration runtime k přenosu dat mezi místním zdrojem dat a cloudem.

Tady je souhrn kroků toku dat pro kopírování pomocí prostředí IR s místním hostováním:

![Přehled toku dat na nejvyšší úrovni](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Vývojář dat vytvoří místní prostředí Integration runtime v rámci objektu pro vytváření dat Azure pomocí rutiny Azure Portal nebo PowerShellu.

2. Vývojář dat vytvoří propojenou službu pro místní úložiště dat. Vývojář to provede zadáním instance prostředí Integration runtime, kterou by služba měla používat pro připojení k úložištím dat.

3. Uzel Integration runtime v místním prostředí šifruje přihlašovací údaje pomocí aplikačního programovacího rozhraní (DPAPI) pro Windows Data Protection a ukládá přihlašovací údaje lokálně. Pokud je pro vysokou dostupnost nastaveno více uzlů, přihlašovací údaje jsou dále synchronizovány v jiných uzlech. Každý uzel šifruje pověření pomocí rozhraní DPAPI a ukládá je místně. Synchronizace přihlašovacích údajů je pro vývojáře dat transparentní a zpracovává se v místním prostředí IR.

4. Azure Data Factory komunikuje s prostředím Integration runtime v místním prostředí a plánuje a spravuje úlohy. Komunikace probíhá prostřednictvím řídicího kanálu, který používá sdílené připojení [Azure Relay](../azure-relay/relay-what-is-it.md#wcf-relay) . Když je potřeba spustit úlohu aktivity, Data Factory zařadí do fronty požadavek spolu s dalšími informacemi o přihlašovacích údajích. V takovém případě přihlašovací údaje nejsou uložené v místním prostředí Integration runtime. Prostředí Integration runtime v místním prostředí spustí úlohu po jejím dotazování do fronty.

5. Místní prostředí Integration runtime kopíruje data mezi místním úložištěm a cloudovým úložištěm. Směr kopírování závisí na způsobu konfigurace aktivity kopírování v datovém kanálu. Pro tento krok se v místním prostředí Integration runtime přímo komunikuje s cloudovou službou úložiště, jako je Azure Blob Storage přes zabezpečený kanál HTTPS.

## <a name="prerequisites"></a>Požadavky

- Podporované verze Windows jsou:
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

Instalace místního prostředí Integration runtime na řadič domény se nepodporuje.

- Místní prostředí Integration runtime vyžaduje 64 operační systém s .NET Framework 4.7.2 nebo novějším. Podrobnosti najdete v tématu [.NET Framework systémových požadavků](/dotnet/framework/get-started/system-requirements) .
- Doporučená minimální konfigurace pro počítač s místním prostředím Integration runtime je procesor 2 GHz se 4 jádry, 8 GB paměti RAM a 80 GB volného místa na pevném disku. Podrobnosti o požadavcích na systém najdete v tématu [stažení](https://www.microsoft.com/download/details.aspx?id=39717).
- Pokud se hostitelský počítač přepne do režimu hibernace, místní prostředí Integration runtime nereaguje na požadavky na data. Nakonfigurujte příslušné schéma napájení v počítači před instalací prostředí Integration runtime v místním prostředí. Pokud je počítač nakonfigurovaný do režimu hibernace, vyzve vás instalační program modulu Integration runtime v místním prostředí se zprávou.
- Abyste mohli úspěšně nainstalovat a nakonfigurovat Integration runtime v místním prostředí, musíte být správcem počítače.
- Spuštění aktivity kopírování probíhá s určitou frekvencí. Využití procesoru a paměti RAM na počítači se řídí stejným vzorem, ve kterém jsou špičky a časy nečinnosti. Využití prostředků také závisí na množství dat, která se přesunují. Když probíhají více úloh kopírování, vidíte využití prostředků v době špičky.
- Úlohy mohou selhat během extrakce dat ve formátech Parquet, ORC nebo Avro. Další informace o Parquet najdete v tématu [Formát Parquet v Azure Data Factory](./format-parquet.md#using-self-hosted-integration-runtime). Vytváření souborů běží na místním počítači pro integraci. Aby bylo možné fungovat podle očekávání, vytváření souborů vyžaduje následující požadavky:
  - [Visual C++ 2010 Redistributable](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Balíček (x64)
  - Běhový modul Java (JRE) verze 8 od poskytovatele JRE, jako je třeba [přijmout OpenJDK](https://adoptopenjdk.net/). Zajistěte, aby byla `JAVA_HOME` Proměnná prostředí nastavena na SLOŽKU JRE (a ne jen na SLOŽKU JDK).

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Nastavení prostředí Integration runtime v místním prostředí

Chcete-li vytvořit a nastavit prostředí Integration runtime v místním prostředí, použijte následující postupy.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Vytvoření prostředí IR s místním hostováním pomocí Azure PowerShell

1. Pro tuto úlohu můžete použít Azure PowerShell. Tady je příklad:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Stáhněte](https://www.microsoft.com/download/details.aspx?id=39717) a nainstalujte prostředí Integration runtime v místním prostředí do místního počítače.

3. Načtěte ověřovací klíč a zaregistrujte modul runtime integrace v místním prostředí s klíčem. Tady je příklad PowerShellu:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Vytvoření prostředí IR v místním prostředí pomocí Azure Data Factory uživatelského rozhraní

Pomocí následujících kroků můžete vytvořit prostředí IR v místním prostředí pomocí Azure Data Factory uživatelského rozhraní.

1. Na stránce **Začínáme** v uživatelském rozhraní Azure Data Factory vyberte [kartu spravovat](./author-management-hub.md) z levého podokna.

   ![Tlačítko Správa domovské stránky](media/doc-common-process/get-started-page-manage-button.png)

1. V levém podokně vyberte **modul runtime integrace** a pak vyberte **+ Nový**.

   ![Vytvoření prostředí Integration Runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Na stránce **instalace prostředí Integration runtime** vyberte **Azure,** v místním prostředí a pak vyberte **pokračovat**.

1. Na následující stránce vyberte v místním prostředí **, aby se** vytvořila Self-Hosted IR, a pak vyberte **pokračovat**.
   ![Vytvoření selfhosted IR](media/create-self-hosted-integration-runtime/new-selfhosted-integration-runtime.png)

1. Zadejte název pro IR a vyberte **vytvořit**.

1. Na stránce **instalace prostředí Integration runtime** vyberte odkaz v části **možnost 1** a otevřete tak expresní instalaci na vašem počítači. Případně proveďte ruční nastavení pomocí kroků v části **možnost 2** . Následující pokyny jsou založené na ruční instalaci:

   ![Instalace prostředí Integration Runtime](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Zkopírujte a vložte ověřovací klíč. Vyberte **Stáhnout a nainstalovat modul runtime integrace**.

    1. Na místním počítači s Windows stáhněte místní prostředí Integration Runtime. Spusťte instalační program.

    1. Na stránce **Integration runtime (v místním prostředí) registrace** vložte klíč, který jste předtím uložili, a vyberte **zaregistrovat**.

       ![Registrace prostředí Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Na stránce **nový uzel Integration runtime (v místním prostředí)** vyberte **Dokončit**.

1. Po úspěšné registraci místně hostovaného prostředí Integration runtime se zobrazí následující okno:

    ![Úspěšná registrace](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Nastavení místního prostředí IR na virtuálním počítači Azure pomocí šablony Azure Resource Manager

Instalaci v místním prostředí IR na virtuálním počítači Azure můžete automatizovat pomocí [šablony pro vytvoření samostatného hostitele](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Šablona poskytuje snadný způsob, jak mít plně funkční prostředí IR v místním prostředí v rámci služby Azure Virtual Network. IR má funkce s vysokou dostupností a škálovatelností, pokud je počet uzlů nastavený na 2 nebo vyšší.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Nastavení stávajícího místního hostovaného prostředí IR prostřednictvím místního PowerShellu

Pomocí příkazového řádku můžete nastavit nebo spravovat existující prostředí IR v místním prostředí. Toto využití může zejména přispět k automatizaci instalace a registrace hostitelských uzlů v místním prostředí.

Dmgcmd.exe je součástí instalačního programu pro samoobslužné hostování. Obvykle se nachází ve složce C:\Program Files\Microsoft Integration Runtime\4.0\Shared\. Tato aplikace podporuje různé parametry a lze ji vyvolat prostřednictvím příkazového řádku pomocí skriptů služby Batch pro automatizaci.

Použijte aplikaci následujícím způsobem:

```powershell
dmgcmd ACTION args...
```

Tady jsou podrobnosti o akcích a argumentech aplikace: 

|KROKY|args|Description|
|------|----|-----------|
|`-rn`,<br/>`-RegisterNewNode`|"`<AuthenticationKey>`" ["`<NodeName>`"]|Zaregistrujte místně hostovaný uzel Integration runtime se zadaným ověřovacím klíčem a názvem uzlu.|
|`-era`,<br/>`-EnableRemoteAccess`|"`<port>`" ["`<thumbprint>`"]|Povolením vzdáleného přístupu na aktuálním uzlu nastavte cluster s vysokou dostupností. Nebo povolte nastavení přihlašovacích údajů přímo v místním prostředí IR bez průchodu Azure Data Factory. Provedete to tak, že použijete rutinu **New-AzDataFactoryV2LinkedServiceEncryptedCredential** ze vzdáleného počítače ve stejné síti.|
|`-erac`,<br/>`-EnableRemoteAccessInContainer`|"`<port>`" ["`<thumbprint>`"]|Pokud je uzel spuštěn v kontejneru, povolte vzdálený přístup k aktuálnímu uzlu.|
|`-dra`,<br/>`-DisableRemoteAccess`||Zakáže vzdálený přístup k aktuálnímu uzlu. Pro instalaci s více uzly je potřeba vzdálený přístup. Rutina **New-AzDataFactoryV2LinkedServiceEncryptedCredential** prostředí PowerShell stále funguje i v případě, že je vzdálený přístup zakázán. Toto chování je pravdivé, pokud je rutina spuštěna ve stejném počítači jako uzel IR v místním prostředí.|
|`-k`,<br/>`-Key`|"`<AuthenticationKey>`"|Přepsat nebo aktualizovat předchozí ověřovací klíč Tuto akci buďte opatrní. Váš předchozí místně hostovaný uzel IR může přejít do režimu offline, pokud klíč je nového prostředí Integration runtime.|
|`-gbf`,<br/>`-GenerateBackupFile`|"`<filePath>`" "`<password>`"|Vygeneruje záložní soubor pro aktuální uzel. Záložní soubor zahrnuje přihlašovací údaje klíče uzlu a datového úložiště.|
|`-ibf`,<br/>`-ImportBackupFile`|"`<filePath>`" "`<password>`"|Obnovte uzel ze záložního souboru.|
|`-r`,<br/>`-Restart`||Restartujte hostitelskou službu Integration runtime v místním prostředí.|
|`-s`,<br/>`-Start`||Spusťte hostovanou hostitelskou službu Integration runtime.|
|`-t`,<br/>`-Stop`||Zastavte hostitelskou službu Integration runtime v místním prostředí.|
|`-sus`,<br/>`-StartUpgradeService`||Spusťte službu upgradu místního prostředí Integration runtime.|
|`-tus`,<br/>`-StopUpgradeService`||Zastavte službu upgradu místního prostředí Integration runtime.|
|`-tonau`,<br/>`-TurnOnAutoUpdate`||Zapněte automatickou aktualizaci prostředí Integration runtime pro místní hostování.|
|`-toffau`,<br/>`-TurnOffAutoUpdate`||Vypněte automatickou aktualizaci prostředí Integration runtime pro místní hostování.|
|`-ssa`,<br/>`-SwitchServiceAccount`|"`<domain\user>`" ["`<password>`"]|Nastavte DIAHostService tak, aby běžel jako nový účet. Pro systémové účty a virtuální účty použijte prázdné heslo.|

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Instalace a registrace místního prostředí IR z webu Microsoft Download Center

1. Přejít na [stránku pro stažení modulu runtime integrace Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Vyberte **Stáhnout**, vyberte 64 verzi a vyberte **Další**. Verze 32 není podporována.
3. Spusťte soubor spravované identity přímo nebo ho uložte na pevný disk a spusťte ho.
4. V **uvítacím** okně vyberte jazyk a vyberte **Další**.
5. Přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.
6. Vyberte **složku** pro instalaci prostředí Integration runtime v místním prostředí a vyberte **Další**.
7. Na stránce **připraveno k instalaci** vyberte **instalovat**.
8. Kliknutím na **Dokončit** dokončete instalaci.
9. Použijte k získání ověřovacího klíče prostředí PowerShell. Tady je příklad PowerShellu pro získání ověřovacího klíče:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

10. V okně **registrovat Integration Runtime (v místním prostředí)** Microsoft Integration runtime Configuration Manager na počítači spuštěná, proveďte následující kroky:

    1. Vložte ověřovací klíč do textové oblasti.

    2. Volitelně můžete výběrem **Zobrazit ověřovací klíč** zobrazit text klíče.

    3. Vyberte **Zaregistrovat**.

## <a name="service-account-for-self-hosted-integration-runtime"></a>Účet služby pro prostředí Integration runtime v místním prostředí

Výchozí protokol účtu služby místního prostředí Integration runtime je **NT SERVICE\DIAHostService**. Můžete ji zobrazit ve **službách – > Integration Runtime Service – > vlastnosti – > přihlášení**.

![Účet služby pro prostředí Integration runtime v místním prostředí](media/create-self-hosted-integration-runtime/shir-service-account.png)

Ujistěte se, že účet má oprávnění přihlásit se jako služba. V opačném případě prostředí Integration runtime nelze úspěšně spustit. Můžete kontrolovat oprávnění v **místních zásadách zabezpečení – > nastavení zabezpečení – > místních zásad – > přiřazení uživatelských práv – > přihlášení jako služba** .

![Snímek obrazovky místních zásad zabezpečení – přiřazení uživatelských práv](media/create-self-hosted-integration-runtime/shir-service-account-permission.png)

![Snímek obrazovky s přiřazením uživatelských práv k přihlášení jako služby](media/create-self-hosted-integration-runtime/shir-service-account-permission-2.png)

## <a name="notification-area-icons-and-notifications"></a>Ikony a oznámení oznamovací oblasti

Pokud přesunete kurzor na ikonu nebo zprávu v oznamovací oblasti, můžete zobrazit podrobnosti o stavu prostředí Integration runtime v místním prostředí.

![Oznámení v oznamovací oblasti](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="high-availability-and-scalability"></a>Vysoká dostupnost a škálovatelnost

Místní prostředí Integration runtime můžete přidružit k několika místním počítačům nebo virtuálním počítačům v Azure. Tyto počítače se nazývají uzly. K místnímu prostředí Integration runtime může být přidruženo až čtyři uzly. Výhody, které mají více uzlů na místních počítačích s nainstalovanou bránou pro logickou bránu:

- Vyšší dostupnost prostředí Integration runtime v místním prostředí, takže už není jediným bodem selhání v řešení pro velké objemy dat nebo v integraci dat v cloudu s Data Factory. Tato dostupnost pomáhá zajistit kontinuitu při použití až čtyř uzlů.
- Zvýšení výkonu a propustnosti během přesunu dat mezi místními a cloudovým úložištěm dat. Získejte další informace o [porovnání výkonu](copy-activity-performance.md).

Můžete přidružit více uzlů instalací softwaru místního prostředí Integration runtime z [webu Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Pak ji Zaregistrujte pomocí některého z ověřovacích klíčů, které byly získány z rutiny **New-AzDataFactoryV2IntegrationRuntimeKey** , jak je popsáno v tomto [kurzu](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> K přidružení jednotlivých uzlů není nutné vytvářet nové prostředí Integration runtime v místním prostředí. Místní prostředí Integration runtime můžete nainstalovat na jiný počítač a zaregistrovat ho pomocí stejného ověřovacího klíče.

> [!NOTE]
> Než přidáte další uzel pro vysokou dostupnost a škálovatelnost, ujistěte se, že je v prvním uzlu povolený možnost **vzdálený přístup k intranetu** . Provedete to tak, že vyberete **Microsoft Integration runtime Configuration Manager**  >  **Nastavení**  >  **vzdáleného přístupu k intranetu**.

### <a name="scale-considerations"></a>Požadavky na škálování

#### <a name="scale-out"></a>Horizontální navýšení kapacity

Když je využití procesoru vysoké a v místním prostředí IR je k dispozici málo dostupné paměti, přidejte nový uzel, abyste mohli lépe škálovat zatížení napříč počítači. Pokud dojde k selhání aktivit, protože časový limit vypršel nebo je v místním prostředí IR hostitelský uzel v režimu offline, pomůže vám to, když do brány přidáte uzel.

#### <a name="scale-up"></a>Vertikální navýšení kapacity

Když procesor a dostupná paměť RAM nejsou dostatečně využité, ale spuštění souběžných úloh dosáhne limitů uzlu, navýšení kapacity zvyšuje počet souběžných úloh, které může uzel běžet. Možná budete chtít škálovat i v případě, že dojde k vypršení časového limitu aktivit, protože prostředí IR v místním prostředí je přetížené. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacitu uzlu:  

![Zvýšit počet souběžných úloh, které mohou být spuštěny na uzlu](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL

Tady jsou požadavky na certifikát TLS/SSL, který používáte k zabezpečení komunikace mezi uzly Integration Runtime:

- Certifikát musí být veřejně důvěryhodný certifikát x509 v3. Doporučujeme používat certifikáty, které jsou vydané certifikační autoritou (CA) veřejných partnerů.
- Každý uzel Integration runtime musí důvěřovat tomuto certifikátu.
- Nedoporučujeme používat certifikáty alternativních názvů subjektu (SAN), protože se používá jenom poslední položka sítě SAN. Všechny ostatní položky SAN se ignorují. Pokud máte například certifikát SAN, jehož sítě San jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, můžete tento certifikát použít pouze na počítači, jehož plně kvalifikovaný název domény (FQDN) je **node2.domain.contoso.com**.
- Certifikát může používat libovolnou velikost klíče podporovanou systémem Windows Server 2012 R2 pro certifikáty TLS/SSL.
- Certifikáty, které používají klíče CNG, nejsou podporované.  

> [!NOTE]
> Tento certifikát se používá:
>
> - K šifrování portů v místním hostovaném uzlu IR.
> - Pro komunikaci mezi uzly a stavovou synchronizací, která zahrnuje synchronizaci přihlašovacích údajů propojených služeb napříč uzly.
> - Když se v místní síti použije rutina PowerShellu pro nastavení přihlašovacích údajů propojené služby.
>
> Tento certifikát doporučujeme používat v případě, že vaše prostředí vaší privátní sítě není zabezpečené nebo pokud chcete zabezpečit komunikaci mezi uzly v rámci vaší privátní sítě.
>
> Přesun dat při přenosu z místního prostředí IR do jiných úložišť dat se vždy provádí v rámci šifrovaného kanálu bez ohledu na to, jestli je tento certifikát nastavený nebo ne.

## <a name="proxy-server-considerations"></a>Požadavky na proxy server

Pokud vaše firemní síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte prostředí Integration runtime v místním prostředí tak, aby používalo příslušné nastavení proxy serveru. Proxy server můžete nastavit během fáze prvotní registrace.

![Zadat proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Při nakonfigurovaném prostředí Integration runtime v místním prostředí používá proxy server k připojení ke zdroji a cíli cloudové služby (pomocí protokolu HTTP nebo HTTPS). To je důvod, proč při počátečním nastavení vybrat **odkaz změnit** .

![Nastavit proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Existují tři možnosti konfigurace:

- **Nepoužívat proxy**: místní prostředí Integration runtime explicitně nepoužívá žádný proxy server pro připojení ke cloudovým službám.
- **Použít systémový proxy server**: modul Integration runtime v místním prostředí používá nastavení proxy, které je nakonfigurované v diahost.exe.config a diawp.exe.config. Pokud tyto soubory neobsahují konfiguraci proxy serveru, místní prostředí Integration runtime se ke cloudové službě připojuje přímo bez serveru proxy.
- **Použití vlastního proxy serveru**: Nakonfigurujte nastavení proxy serveru http tak, aby se používalo pro místní prostředí Integration runtime, namísto použití konfigurací v diahost.exe.config a diawp.exe.config. **Adresy** a hodnoty **portů** jsou povinné. Hodnoty **uživatelského jména** a **hesla** jsou volitelné, v závislosti na nastavení ověřování proxy serveru. Všechna nastavení jsou šifrovaná pomocí rozhraní Windows DPAPI v místním prostředí Integration runtime a uložená místně na počítači.

Po uložení aktualizovaných nastavení proxy serveru se služba Host prostředí Integration runtime automaticky restartuje.

Pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru, můžete po registraci místního prostředí Integration runtime použít Microsoft Integration Runtime Configuration Manager.

1. Otevřete **Microsoft Integration Runtime Configuration Manager**.
1. Vyberte kartu **Settings** (Nastavení).
1. V části **proxy server http** vyberte odkaz **změnit** a otevřete dialogové okno **nastavit proxy server http** .
1. Vyberte **Další**. Zobrazí se upozornění s dotazem na vaše oprávnění k uložení nastavení proxy serveru a opětovném spuštění hostitelské služby modulu runtime integrace.

Pomocí nástroje Configuration Manager můžete zobrazit a aktualizovat proxy server HTTP.

![Zobrazit a aktualizovat proxy server](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Pokud nastavíte proxy server s ověřováním NTLM, spustí se služba hostitele modulu runtime integrace v rámci účtu domény. Pokud později změníte heslo pro účet domény, nezapomeňte aktualizovat nastavení konfigurace služby a službu restartovat. Z tohoto důvodu doporučujeme, abyste měli přístup k proxy server pomocí vyhrazeného účtu domény, který nevyžaduje, abyste heslo aktualizovali často.

### <a name="configure-proxy-server-settings"></a>Konfigurace nastavení proxy server

Pokud pro proxy server HTTP vyberete možnost **použít systém proxy** , místní prostředí Integration runtime používá nastavení proxy serveru v diahost.exe.config a diawp.exe.config. Pokud tyto soubory neurčují žádný proxy server, místní prostředí Integration runtime se ke cloudové službě připojí přímo, aniž by museli procházet proxy serverem. Následující postup poskytuje pokyny k aktualizaci diahost.exe.config souboru:

1. V Průzkumníku souborů udělejte zabezpečenou kopii složky C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config jako zálohu původního souboru.
1. Otevřete Poznámkový blok spuštěný jako správce.
1. V programu Poznámkový blok otevřete textový soubor C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config.
1. Najděte výchozí značku **System.NET** , jak je znázorněno v následujícím kódu:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Pak můžete přidat proxy server podrobnosti, jak je znázorněno v následujícím příkladu:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Značka proxy umožňuje dalším vlastnostem zadat požadovaná nastavení `scriptLocation` . Syntaxe naleznete v tématu [ \<proxy\> element (nastavení sítě)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) .

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. Uložte konfigurační soubor do původního umístění. Pak restartujte hostitelskou službu Integration runtime v místním prostředí, která změny převezme.

   Chcete-li službu restartovat, použijte aplet služby z ovládacích panelů. Nebo z Integration Runtime Configuration Manager, vyberte tlačítko **Zastavit službu** a pak vyberte **Spustit službu**.

   Pokud se služba nespustí, pravděpodobně jste do konfiguračního souboru aplikace, který jste upravili, přidali nesprávnou syntaxi značek XML.

> [!IMPORTANT]
> Nezapomeňte aktualizovat diahost.exe.config i diawp.exe.config.

Musíte se také ujistit, že Microsoft Azure v seznamu povolených společností. Seznam platných IP adres Azure si můžete stáhnout z [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Možné příznaky pro problémy související s bránou firewall a proxy server

Pokud se zobrazí chybové zprávy podobné následujícímu, příčinou je pravděpodobně nesprávná konfigurace brány firewall nebo proxy server. Taková konfigurace brání místnímu prostředí Integration runtime, aby se mohl připojit k Data Factory a sám se ověřit. Pokud chcete zajistit správnou konfiguraci brány firewall a proxy server, přečtěte si předchozí část.

- Při pokusu o registraci místního prostředí Integration runtime se zobrazí následující chybová zpráva: "nepovedlo se zaregistrovat tento Integration Runtime uzel. Potvrďte, že ověřovací klíč je platný a že je na tomto počítači spuštěná služba hostitele integrační služby.
- Když otevřete Integration Runtime Configuration Manager, zobrazí se stav **Odpojeno** nebo **připojení**. Když zobrazíte protokoly událostí Windows, v části **Prohlížeč událostí**  >  **protokoly aplikací a služeb**  >  **Microsoft Integration runtime** se zobrazí chybové zprávy, jako je tato:

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

### <a name="enable-remote-access-from-an-intranet"></a>Povolení vzdáleného přístupu z intranetu

Pokud používáte PowerShell k šifrování přihlašovacích údajů ze síťového počítače, který není nainstalovaný v místním prostředí Integration runtime, můžete povolit možnost **vzdálený přístup z intranetu** . Pokud spustíte prostředí PowerShell k šifrování přihlašovacích údajů v počítači, na který jste nainstalovali místní prostředí Integration runtime, nemůžete povolit **vzdálený přístup z intranetu**.

Před přidáním dalšího uzlu pro vysokou dostupnost a škálovatelnost povolte **vzdálený přístup z intranetu** .  

Při spuštění instalačního programu Integration runtime v místním prostředí verze 3,3 nebo novější ve výchozím nastavení zakáže Instalační program modulu Integration runtime v místním prostředí **vzdálený přístup z intranetu** na počítači místního prostředí Integration runtime.

Pokud používáte bránu firewall od partnera nebo jiné, můžete ručně otevřít port 8060 nebo port konfigurovaný uživatelem. Pokud máte problém s bránou firewall při nastavování místního prostředí Integration runtime, použijte následující příkaz pro instalaci místního prostředí Integration runtime bez konfigurace brány firewall:

```cmd
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Pokud se rozhodnete neotevírat port 8060 na počítači prostředí Integration runtime v místním prostředí, nakonfigurujte přihlašovací údaje datového úložiště pomocí jiných mechanismů, než je aplikace s nastavením přihlašovacích údajů. Můžete například použít rutinu **New-AzDataFactoryV2LinkedServiceEncryptCredential** prostředí PowerShell.

## <a name="ports-and-firewalls"></a>Porty a brány firewall

Je potřeba vzít v úvahu dvě brány firewall:

- *Podniková brána firewall* , která běží v centrálním směrovači organizace
- *Brána Windows Firewall* konfigurovaná jako démon na místním počítači, kde je nainstalován místní prostředí Integration runtime

![Brány firewall](media/create-self-hosted-integration-runtime/firewall.png)

Na úrovni brány firewall pro podnikové sítě je potřeba nakonfigurovat následující domény a odchozí porty:

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-internal.md)]

Na úrovni brány firewall systému Windows nebo počítači jsou tyto Odchozí porty obvykle povoleny. Pokud ne, můžete nakonfigurovat domény a porty na počítači prostředí Integration runtime v místním prostředí.

> [!NOTE]
> Protože v současné době Azure Relay nepodporuje tag služby, musíte pro komunikaci Azure Relay použít službu Service tag **AzureCloud** nebo **Internet** v pravidlech NSG.
> Pro komunikaci Azure Data Factory můžete v nastavení pravidla NSG použít Tag služby **DataFactoryManagement** .

Na základě vašich zdrojů a jímky možná budete muset v podnikové bráně firewall nebo bráně Windows Firewall zapnout další domény a odchozí porty.

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-external.md)]

U některých cloudových databází, jako jsou Azure SQL Database a Azure Data Lake, možná budete muset v konfiguraci brány firewall zapnout IP adresy počítačů s místním prostředím Integration runtime.

### <a name="get-url-of-azure-relay"></a>Získat adresu URL Azure Relay

Jedna požadovaná doména a port, které musí být vloženy do seznamu povolených bran firewall, je pro komunikaci Azure Relay. Místní prostředí Integration runtime používá ho pro interaktivní vytváření, jako je například test Connection, procházení seznamu složek a seznam tabulek, získání schématu a náhled dat. Pokud nechcete povolit **. ServiceBus.Windows.NET** a chcete mít konkrétnější adresy URL, můžete zobrazit všechny plně kvalifikované názvy domén, které jsou vyžadovány místním prostředím Integration runtime z portálu ADF. Postupujte takto:

1. Přejít na portál ADF a vyberte místní prostředí Integration runtime.
2. Na stránce Upravit vyberte **uzly**.
3. Vyberte **Zobrazit adresy URL služby** pro získání všech plně kvalifikovaných názvů domén.

   ![Azure Relay adresy URL](media/create-self-hosted-integration-runtime/Azure-relay-url.png)

4. Tyto plně kvalifikované názvy domény můžete přidat do seznamu povolených pravidel brány firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopírování dat ze zdroje do jímky

Ujistěte se, že jste správně povolili pravidla brány firewall pro podnikovou bránu firewall, bránu Windows Firewall počítače v místním prostředí Integration runtime a samotné úložiště dat. Povolení těchto pravidel umožňuje, aby se místní prostředí Integration runtime úspěšně připojovalo ke zdroji i jímky. Povolte pravidla pro každé úložiště dat, které je součástí operace kopírování.

Pokud například chcete kopírovat z místního úložiště dat do jímky SQL Database nebo do jímky Azure synapse Analytics, proveďte následující kroky:

1. Povolí odchozí komunikaci TCP na portu 1433 pro bránu firewall systému Windows i pro podnikovou bránu firewall.
2. Nakonfigurujte nastavení brány firewall SQL Database a přidejte tak IP adresu počítače místního prostředí Integration runtime do seznamu povolených IP adres.

> [!NOTE]
> Pokud brána firewall nepovoluje odchozí port 1433, místní prostředí Integration runtime nemůže získat přímý přístup k databázi SQL. V takovém případě můžete použít [dvoufázové kopírování](copy-activity-performance.md) SQL Database a Azure synapse Analytics. V tomto scénáři budete pro přesun dat potřebovat jenom HTTPS (port 443).

## <a name="installation-best-practices"></a>Osvědčené postupy instalace

Místní prostředí Integration runtime můžete nainstalovat stažením balíčku pro instalaci spravované identity z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Podrobné pokyny najdete v článku [přesunutí dat mezi místním prostředím a cloudem](tutorial-hybrid-copy-powershell.md) .

- Nakonfigurujte schéma napájení na hostitelském počítači pro místní prostředí Integration runtime, aby se počítač nev režimu hibernace. Pokud se hostitelský počítač přepne do režimu hibernace, bude místní prostředí Integration runtime offline.
- Pravidelně zálohujte přihlašovací údaje spojené s místním prostředím Integration runtime.
- Informace o automatizaci operací nastavení IR v místním prostředí najdete v tématu [nastavení existujícího prostředí IR s místním hostováním pomocí PowerShellu](#setting-up-a-self-hosted-integration-runtime).

## <a name="next-steps"></a>Další kroky

Podrobné pokyny najdete v tématu [kurz: kopírování místních dat do cloudu](tutorial-hybrid-copy-powershell.md).
