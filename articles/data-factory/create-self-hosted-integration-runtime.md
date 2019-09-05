---
title: Vytvoření prostředí Integration runtime v místním prostředí v Azure Data Factory | Microsoft Docs
description: Naučte se, jak vytvořit místní prostředí Integration runtime v Azure Data Factory, které umožňuje objektům pro přístup k datovým úložištím v privátní síti.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: be59f5fd34c52397b54146a8aeaf51f4d594452f
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383357"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Vytvoření a konfigurace prostředí Integration runtime v místním prostředí
Prostředí Integration runtime (IR) je výpočetní infrastruktura, kterou Azure Data Factory používá k poskytování funkcí pro integraci dat napříč různými síťovými prostředími. Podrobnosti o IR najdete v tématu [Přehled prostředí Integration runtime](concepts-integration-runtime.md).

Místní prostředí Integration runtime může spouštět aktivity kopírování mezi cloudovým úložištěm dat a úložištěm dat v privátní síti a může odesílat transformační aktivity pro výpočetní prostředky v místní síti nebo ve službě Azure Virtual Network. Instalace prostředí Integration runtime v místním prostředí v místním počítači nebo virtuálním počítači (VM) v privátní síti.  

Tento dokument popisuje, jak můžete vytvořit a nakonfigurovat prostředí IR v místním prostředí.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Kroky vysoké úrovně pro instalaci prostředí IR s místním hostováním
1. Vytvořte místní prostředí Integration Runtime. Pro tuto úlohu můžete použít Azure Data Factory uživatelské rozhraní. Tady je příklad PowerShellu:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Stáhněte](https://www.microsoft.com/download/details.aspx?id=39717) a nainstalujte prostředí Integration runtime v místním prostředí do místního počítače.

3. Načtěte ověřovací klíč a zaregistrujte modul runtime integrace v místním prostředí s klíčem. Tady je příklad PowerShellu:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Nastavení místního prostředí IR na virtuálním počítači Azure pomocí šablony Azure Resource Manager 
Pomocí [této šablony Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)můžete automatizovat nastavení místního prostředí IR na virtuálním počítači Azure. Tato šablona poskytuje snadný způsob, jak zajistit plně funkční prostředí IR v místním prostředí v rámci virtuální sítě Azure s funkcemi pro vysokou dostupnost a škálovatelnost (Pokud jste nastavili počet uzlů na 2 nebo vyšší).

## <a name="command-flow-and-data-flow"></a>Tok příkazů a tok dat
Když přesouváte data mezi místním prostředím a cloudem, aktivita používá místní prostředí Integration runtime k přenosu dat z místního zdroje dat do cloudu a naopak.

Tady je datový tok vysoké úrovně pro Shrnutí kroků kopírování pomocí místního prostředí IR:

![Podrobný přehled](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Vývojář dat vytvoří místní prostředí Integration runtime v rámci objektu pro vytváření dat Azure pomocí rutiny prostředí PowerShell. V současné době Azure Portal tuto funkci nepodporuje.
2. Vývojář dat vytvoří propojenou službu pro místní úložiště dat zadáním instance prostředí Integration runtime v místním prostředí, kterou by měl použít pro připojení k úložištím dat.
3. Uzel Integration runtime v místním prostředí šifruje přihlašovací údaje pomocí aplikačního programovacího rozhraní (DPAPI) pro Windows Data Protection a ukládá přihlašovací údaje lokálně. Pokud je pro vysokou dostupnost nastaveno více uzlů, přihlašovací údaje jsou dále synchronizovány v jiných uzlech. Každý uzel šifruje pověření pomocí rozhraní DPAPI a ukládá je místně. Synchronizace přihlašovacích údajů je pro vývojáře dat transparentní a zpracovává se v místním prostředí IR.    
4. Služba Data Factory komunikuje s místním prostředím Integration runtime pro plánování a správu úloh prostřednictvím *řídicího kanálu* , který používá sdílený [Azure Service Bus Relay](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay). Když je potřeba spustit úlohu aktivity, Data Factory zařadí do fronty požadavek spolu s dalšími informacemi o přihlašovacích údajích (pro případ, že přihlašovací údaje ještě nejsou uložené v místním prostředí Integration Runtime). Modul runtime integrace v místním prostředí se po cyklickém dotazování fronty dotáže na úlohu.
5. Místní prostředí Integration runtime kopíruje data z místního úložiště do cloudového úložiště nebo naopak v závislosti na tom, jak je aktivita kopírování nakonfigurovaná v datovém kanálu. Pro tento krok se v místním prostředí Integration runtime přímo komunikuje se službami cloudového úložiště, jako je Azure Blob Storage přes zabezpečený kanál (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Pokyny k používání prostředí IR v místním prostředí

- Jeden místní prostředí Integration runtime lze použít pro více místních zdrojů dat. Jeden místní prostředí Integration runtime může být sdíleno s jinou datovou továrnou v rámci stejného Azure Active Directory tenanta. Další informace najdete v tématu [sdílení prostředí Integration runtime](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories)v místním prostředí.
- V jednom počítači může být nainstalovaná jenom jedna instance prostředí Integration runtime v místním prostředí. Pokud máte dva datové továrny, které potřebují přístup k místním zdrojům dat, pomocí [funkce sdílení IR](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories) v místním prostředí můžete sdílet místně hostovaný modul Integration runtime nebo nainstalovat místní prostředí Integration runtime na dva místní počítače, jednu pro jednotlivé datové továrny.  
- Místní prostředí Integration runtime nemusí být ve stejném počítači jako zdroj dat. Nicméně díky místnímu prostředí Integration runtime, které je bližší ke zdroji dat, zkracuje dobu, po kterou se místní prostředí Integration runtime připojí ke zdroji dat. Doporučujeme nainstalovat modul runtime integrace v místním prostředí do počítače, který je jiný než ten, který je hostitelem místního zdroje dat. Pokud je místní prostředí Integration runtime a zdroj dat na různých počítačích, Integration runtime v místním prostředí nesoutěží o prostředky se zdrojem dat.
- Můžete mít několik místních prostředí Integration runtime na různých počítačích, které se připojují ke stejnému místnímu zdroji dat. Například můžete mít dva místně hostované prostředí Integration runtime, které obsluhují dva datové továrny, ale stejný místní zdroj dat je zaregistrován jak v datové továrně.
- Pokud již máte v počítači nainstalovánu bránu, aby mohla sloužit jako Power BI scénář, nainstalujte samostatný modul runtime integrace pro místní prostředí pro Azure Data Factory na jiném počítači.
- Místní prostředí Integration runtime se musí použít k podpoře integrace dat v rámci virtuální sítě Azure.
- Považovat zdroj dat za místní zdroj dat, který je za bránou firewall, a to i v případě, že používáte Azure ExpressRoute. K navázání připojení mezi službou a zdrojem dat použijte místní prostředí Integration runtime.
- Místní prostředí Integration runtime je nutné použít i v případě, že je úložiště dat v cloudu na virtuálním počítači Azure IaaS.
- Úlohy můžou selhat v místním prostředí Integration runtime, které je nainstalované na Windows serveru, na kterém je povolené šifrování kompatibilní se standardem FIPS. Pokud chcete tento problém obejít, zakažte na serveru šifrování standardu FIPS. Pokud chcete zakázat šifrování kompatibilní se standardem FIPS, změňte následující hodnotu registru z 1 (povoleno) na 0 (zakázáno `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`):.

## <a name="prerequisites"></a>Požadavky

- Podporované verze operačního systému jsou Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 a Windows Server 2019. Instalace prostředí Integration runtime v místním prostředí není na řadiči domény podporována.
- Vyžaduje se .NET Framework 4.6.1 nebo novější. Pokud instalujete místní prostředí Integration runtime na počítač se systémem Windows 7, nainstalujte .NET Framework 4.6.1 nebo novější. Podrobnosti najdete v tématu [.NET Framework systémových požadavků](/dotnet/framework/get-started/system-requirements) .
- Doporučená konfigurace pro počítač s místním prostředím Integration runtime je aspoň 2 GHz, čtyři jádra, 8 GB paměti RAM a 80 GB disku.
- Pokud se hostitelský počítač přepne do režimu hibernace, místní prostředí Integration runtime nereaguje na požadavky na data. Nakonfigurujte příslušné schéma napájení v počítači před instalací prostředí Integration runtime v místním prostředí. Pokud je počítač nakonfigurovaný do režimu hibernace, zobrazí se při instalaci místního prostředí Integration runtime zpráva.
- Abyste mohli úspěšně nainstalovat a nakonfigurovat prostředí Integration runtime v místním prostředí, musíte být správcem počítače.
- Spuštění aktivity kopírování probíhá na konkrétní frekvenci. Využití prostředků (CPU, paměť) na počítači se řídí stejným vzorem v době špičky a nečinnosti. Využití prostředků také závisí do značné míry na množství dat, který se přesouvá. Když probíhají více úloh kopírování, vidíte využití prostředků v době špičky.
- Úkoly mohou selhat při extrakci dat ve formátech Parquet, ORC nebo Avro. Vytváření souborů běží na místním počítači pro integraci a vyžaduje, aby následující požadavky fungovaly podle očekávání ( [ve Azure Data Factory ve formátu Parquet](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Distribuovatelný C++ balíček Visual 2010](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) (x64)
    - Běhový modul Java (JRE) verze 8 od poskytovatele JRE, jako je třeba [přijmout OpenJDK](https://adoptopenjdk.net/), `JAVA_HOME` čímž se zajistí, že se nastaví proměnná prostředí.

## <a name="installation-best-practices"></a>Osvědčené postupy pro instalaci
Místní prostředí Integration runtime můžete nainstalovat stažením instalačního balíčku MSI z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Podrobné pokyny najdete v [článku přesunutí dat mezi místním a cloudovým článkem](tutorial-hybrid-copy-powershell.md) .

- Nakonfigurujte schéma napájení na hostitelském počítači pro prostředí Integration runtime v místním prostředí, aby se počítač nev režimu hibernace. Pokud se hostitelský počítač přepne do režimu hibernace, bude místní prostředí Integration runtime offline.
- Pravidelně zálohujte přihlašovací údaje přidružené k prostředí Integration runtime pro místní hostování.
- Informace o automatizaci operací instalace prostředí IR pro místní hostování najdete v [části níže](#automation-support-for-self-hosted-ir-function).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Instalace a registrace místního prostředí IR z webu Download Center

1. Přejít na [stránku pro stažení modulu runtime integrace Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Vyberte **Stáhnout**, vyberte 64 verze (32-bit není podporován) a vyberte **Další**.
3. Spusťte soubor MSI přímo nebo ho uložte na pevný disk a spusťte ho.
4. Na stránce **Vítejte** vyberte jazyk a vyberte **Další**.
5. Přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.
6. Vyberte **složku** pro instalaci prostředí Integration runtime v místním prostředí a vyberte **Další**.
7. Na stránce **připraveno k instalaci** vyberte **instalovat**.
8. Instalaci dokončíte kliknutím na **Dokončit** .
9. Získejte ověřovací klíč pomocí Azure PowerShell. Tady je příklad PowerShellu pro získání ověřovacího klíče:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na stránce **registrovat Integration Runtime (v místním prostředí)** Microsoft Integration runtime Configuration Manager na počítači spuštěná, proveďte následující kroky:

    a. Vložte ověřovací klíč do textové oblasti.

    b. Volitelně můžete výběrem **Zobrazit ověřovací klíč** zobrazit text klíče.

    c. Vyberte **Zaregistrovat**.

## <a name="automation-support-for-self-hosted-ir-function"></a>Podpora automatizace pro funkci IR hostovaná v místním prostředí


> [!NOTE]
> Pokud plánujete nastavit prostředí IR v místním prostředí na virtuálním počítači Azure a chcete tuto instalaci automatizovat pomocí šablon Azure Resource Manager, přečtěte si [část](#setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template).

Pomocí příkazového řádku můžete nastavit nebo spravovat existující prostředí IR v místním prostředí. Tato funkce se dá použít hlavně pro automatizaci instalace a registraci v místních uzlech IR. 

**Dmgcmd. exe** je součástí instalace v místním prostředí, obvykle se nachází v těchto případech: C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ folder. Tato možnost podporuje různé parametry a lze ji vyvolat prostřednictvím příkazového řádku pomocí skriptů služby Batch pro automatizaci. 

*Použití:* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Podrobnosti (parametry/vlastnost):* 

| Vlastnost                                                    | Popis                                                  | Požadováno |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Registrovat Integration Runtime (v místním prostředí) uzel se zadaným ověřovacím klíčem | Ne       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Povolení vzdáleného přístupu v aktuálním uzlu pro nastavení clusteru s vysokou dostupností a/nebo povolení nastavení přihlašovacích údajů přímo na místním prostředí IR (bez průchodu přes službu ADF) pomocí  **Rutina New-AzDataFactoryV2LinkedServiceEncryptedCredential** ze vzdáleného počítače ve stejné síti. | Ne       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Povolit vzdálený přístup k aktuálnímu uzlu, když je uzel spuštěný v kontejneru | Ne       |
| DisableRemoteAccess                                         | Zakáže vzdálený přístup k aktuálnímu uzlu. Pro instalaci s více uzly je nutný vzdálený přístup. Rutina New-**AzDataFactoryV2LinkedServiceEncryptedCredential** prostředí PowerShell stále funguje i v případě, že je vzdálený přístup zakázán, pokud je spuštěný ve stejném počítači jako uzel IR v místním prostředí. | Ne       |
| Klíč "`<AuthenticationKey>`"                                 | Přepsat/aktualizovat předchozí ověřovací klíč. Buďte opatrní, protože to může vést k tomu, že váš předchozí místně hostovaný uzel IR přejde do režimu offline, pokud je klíč nového prostředí Integration runtime. | Ne       |
| GenerateBackupFile "`<filePath>``<password>`" ""            | Vygenerujte záložní soubor pro aktuální uzel. záložní soubor zahrnuje přihlašovací údaje klíče uzlu a úložiště dat. | Ne       |
| ImportBackupFile "`<filePath>``<password>`" ""              | Obnovení uzlu ze záložního souboru                          | Ne       |
| Restart                                                     | Restartujte službu Integration Runtime (v místním prostředí) Host.   | Ne       |
| Spustit                                                       | Spusťte službu Integration Runtime (v místním prostředí) Host.     | Ne       |
| Zastavit                                                        | Zastavit službu aktualizace Integration Runtime (v místním prostředí)        | Ne       |
| StartUpgradeService                                         | Spustit službu aktualizace Integration Runtime (v místním prostředí)       | Ne       |
| StopUpgradeService                                          | Zastavit službu aktualizace Integration Runtime (v místním prostředí)        | Ne       |
| TurnOnAutoUpdate                                            | Zapnout Integration Runtime (v místním prostředí) automatické aktualizace        | Ne       |
| TurnOffAutoUpdate                                           | Vypnout automatickou aktualizaci Integration Runtime (v místním prostředí)       | Ne       |
| SwitchServiceAccount "< doména \ Uživatel >" ["heslo"]           | Nastavte DIAHostService tak, aby běžel jako nový účet. Pro systémový účet nebo virtuální účet použít prázdné heslo () | Ne       |
| Loglevel`<logLevel>`                                       | Nastavit úroveň protokolování ETW (vypnuto, chyba, podrobný nebo úplný). Obecně používané podporou Microsoftu při ladění. | Ne       |

   


## <a name="high-availability-and-scalability"></a>Vysoká dostupnost a škálovatelnost
Prostředí Integration runtime v místním prostředí je možné přidružit k několika místním počítačům nebo Virtual Machines v Azure. Tyto počítače se nazývají uzly. K místnímu prostředí Integration runtime může být přidruženo až čtyři uzly. Výhody, které mají více uzlů (místní počítače s nainstalovanou bránou) pro logickou bránu:
* Vyšší dostupnost prostředí Integration runtime v místním prostředí, takže už není jediným bodem selhání v řešení pro velké objemy dat nebo v integraci dat v cloudu s Azure Data Factory, což zajišťuje kontinuitu až po čtyřech uzlech.
* Zvýšení výkonu a propustnosti během přesunu dat mezi místními a cloudovým úložištěm dat. Získejte další informace o [porovnání výkonu](copy-activity-performance.md).

Můžete přidružit více uzlů instalací softwaru místního prostředí Integration runtime z [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=39717). Pak ji Zaregistrujte pomocí některého z ověřovacích klíčů získaných z rutiny **New-AzDataFactoryV2IntegrationRuntimeKey** , jak je popsáno v tomto [kurzu](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Pro přidružení každého uzlu nemusíte vytvářet nový místní prostředí Integration runtime. Místní prostředí Integration runtime můžete nainstalovat na jiný počítač a zaregistrovat ho pomocí stejného ověřovacího klíče. 

> [!NOTE]
> Než přidáte další uzel pro vysokou dostupnost a škálovatelnost, ujistěte se, že je v prvním uzlu povolený možnost **vzdálený přístup k intranetu** ( > **Nastavení**MicrosoftIntegrationruntimeConfigurationManager >  **Vzdálený přístup k intranetu**). 

### <a name="scale-considerations"></a>Požadavky na škálování

#### <a name="scale-out"></a>Horizontální navýšení kapacity

Pokud je dostupná paměť v místním prostředí IR nízká a využití CPU je vysoké, přidání nového uzlu pomáhá škálovat zatížení napříč počítači. Pokud aktivity selžou, protože čekají na vypršení časového limitu nebo protože je uzel IR v místním prostředí v režimu offline, pomůže vám to, když do brány přidáte uzel.

#### <a name="scale-up"></a>Škálovat nahoru

Pokud není dostupná paměť a procesor dobře využité, ale spuštění souběžných úloh dosáhlo limitu, měli byste škálovat výše, a to tak, že zvýšíte počet souběžných úloh, které se můžou spouštět na uzlu. Možná budete chtít škálovat i v případě, že aktivity čekají na vypršení časového limitu, protože prostředí IR v místním prostředí je přetížené. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacitu uzlu:  

![Zvýšení souběžných úloh, které lze spustit na uzlu](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL

Tady jsou požadavky na certifikát TLS/SSL, který se používá k zabezpečení komunikace mezi uzly Integration Runtime:

- Certifikát musí být veřejně důvěryhodný certifikát x509 v3. Doporučujeme používat certifikáty vydávané veřejnou (partnerskou) certifikační autoritou (CA).
- Každý uzel Integration runtime musí důvěřovat tomuto certifikátu.
- Nedoporučujeme certifikáty pro alternativní název subjektu (SAN), protože se použije jenom poslední položka sítě SAN a všechny ostatní budou v důsledku současných omezení ignorovány. Pokud máte například certifikát SAN, jehož sítě San jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, můžete tento certifikát použít pouze na počítači, jehož plně kvalifikovaný název domény je **node2.domain.contoso.com**.
- Certifikát podporuje libovolnou velikost klíče podporovanou systémem Windows Server 2012 R2 pro certifikáty SSL.
- Certifikáty, které používají klíče CNG, se nepodporují.  

> [!NOTE]
> Tento certifikát se používá k šifrování portů v uzlu INFRAČERVENého prostředí v místním prostředí, který se používá pro **komunikaci** mezi uzly (pro synchronizaci stavu, která zahrnuje synchronizaci přihlašovacích údajů propojených služeb napříč uzly) a při **použití rutiny PowerShellu pro. Nastavení přihlašovacích údajů propojené služby** v rámci místní sítě. Tento certifikát doporučujeme použít, pokud vaše prostředí vaší privátní sítě není zabezpečené nebo pokud chcete zabezpečit komunikaci mezi uzly v rámci vaší privátní sítě. Přesun dat při přenosu z místního prostředí IR do jiných úložišť dat vždy probíhá pomocí šifrovaného kanálu bez ohledu na tuto sadu certifikátů. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Sdílení prostředí Integration runtime v místním prostředí s několika objekty pro vytváření dat

Můžete znovu použít stávající infrastrukturu Integration runtime v místním prostředí, kterou jste už nastavili v datové továrně. Díky tomu můžete vytvořit *propojený modul runtime integrace* v místním prostředí v jiném objektu pro vytváření dat odkazem na existující prostředí IR (Shared-Hosted).

Pokud chcete sdílet prostředí Integration runtime v místním prostředí pomocí PowerShellu, přečtěte si téma [Vytvoření sdíleného prostředí Integration runtime v místním prostředí v Azure Data Factory pomocí PowerShellu](create-shared-self-hosted-integration-runtime-powershell.md).

Při dvanácti minutách úvodních a demonstračních funkcí této funkce se podívejte na následující video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologie

- **Sdílený IR**: Původní místní prostředí IR běžící na fyzické infrastruktuře.  
- **Propojené infračervené**prostředí: IR, který odkazuje na jiný sdílený IR. Toto je logický IR a používá infrastrukturu jiného prostředí IR (Shared-Hosted).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Kroky vysoké úrovně pro vytvoření propojeného prostředí IR s místním hostováním

1. V místním prostředí IR, které se má sdílet, udělte oprávnění objektu pro vytváření dat, ve kterém chcete vytvořit propojený IR. 

   ![Tlačítko pro udělení oprávnění na kartě sdílení](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Výběry pro přiřazení oprávnění](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Poznamenejte si ID prostředku pro prostředí IR v místním prostředí, které se má sdílet.

   ![Umístění ID prostředku](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. V objektu pro vytváření dat, do kterého byla udělena oprávnění, vytvořte nové prostředí IR v místním prostředí (propojeno) a zadejte ID prostředku.

   ![Tlačítko pro vytvoření propojeného prostředí Integration runtime v místním prostředí](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Pole pro název a ID prostředku](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Monitorování 

- **Sdílený IR**

  ![Výběry pro vyhledání sdíleného prostředí Integration runtime](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Karta pro monitorování](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Propojený IR**

  ![Výběry pro vyhledání propojeného prostředí Integration runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Karta pro monitorování](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Známá omezení sdílení IR v místním prostředí

* Objekt pro vytváření dat, ve kterém bude vytvořen propojený IR, musí mít soubor [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Ve výchozím nastavení mají datové továrny vytvořené v rutinách Azure Portal nebo PowerShellu implicitně vytvořené MSI. Když ale datovou továrnu vytvoříte pomocí šablony Azure Resource Manager nebo sady SDK, musí být vlastnost **identity** nastavena explicitně, aby se zajistilo, že Azure Resource Manager vytvoří datovou továrnu, která obsahuje soubor MSI. 

* Sada Azure Data Factory .NET SDK, která podporuje tuto funkci, je verze 1.1.0 nebo novější.

* K udělení oprávnění uživatel potřebuje roli vlastníka nebo zděděnou roli vlastníka v objektu pro vytváření dat, kde se nachází sdílený IR.

* Funkce sdílení funguje pouze pro datové továrny v rámci stejného Azure Active Directory tenanta.

* Pro [uživatele typu Host](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)služby Active Directory funkce vyhledávání (výpis všech datových továren pomocí klíčového slova hledání) v uživatelském rozhraní [nefunguje](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). I když je uživatel typu Host vlastníkem služby Data Factory, může tento IR sdílet bez funkcionality hledání, a to tak, že přímo zadáte soubor MSI objektu pro vytváření dat, se kterým se musí tento IR sdílet, a to tak, že v textovém poli **přiřadit oprávnění** kliknete na **Přidat** . v uživatelském rozhraní Azure Data Factory. 

  > [!NOTE]
  > Tato funkce je k dispozici pouze v Azure Data Factory v2. 

## <a name="notification-area-icons-and-notifications"></a>Ikony a oznámení oznamovací oblasti

Pokud přesunete kurzor na ikonu nebo zprávu v oznamovací oblasti, můžete najít podrobnosti o stavu prostředí Integration runtime v místním prostředí.

![Oznámení v oznamovací oblasti](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Porty a brána firewall
Existují dvě brány firewall, které je potřeba vzít v úvahu: *podniková brána firewall* běžící v centrálním směrovači organizace a *Brána Windows Firewall* nakonfigurovaná jako démon na místním počítači, na kterém je nainstalovaný modul runtime integrace v místním prostředí.

![Brána firewall](media/create-self-hosted-integration-runtime/firewall.png)

Na úrovni *brány firewall pro podnikové sítě* je potřeba nakonfigurovat následující domény a odchozí porty:

Názvy domén | Porty | Popis
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Používá se pro komunikaci s back-end službou pro přesun dat.
*.core.windows.net | 443 | Používá se pro připravené kopírování prostřednictvím úložiště objektů BLOB v Azure (Pokud je nakonfigurované).
*.frontend.clouddatahub.net | 443 | Používá se pro komunikaci s back-end službou pro přesun dat.
download.microsoft.com | 443 | Používá se ke stahování aktualizací.

Na úrovni *brány Windows Firewall* (na úrovni počítače) jsou tyto Odchozí porty obvykle povoleny. V takovém případě můžete podle potřeby nakonfigurovat domény a porty na počítači prostředí Integration runtime v místním prostředí.

> [!NOTE]
> Na základě vašich zdrojů a umyvadel možná budete muset povolit další domény a odchozí porty ve vaší podnikové bráně firewall nebo bráně Windows Firewall.
>
> U některých cloudových databází (například Azure SQL Database a Azure Data Lake) možná budete muset v konfiguraci brány firewall seznam povolených IP adres počítačů s místním prostředím Integration runtime.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopírování dat ze zdroje do jímky
Ujistěte se, že pravidla brány firewall jsou v podnikové bráně firewall povolená správně, brána Windows Firewall na počítači místního prostředí Integration runtime a samotné úložiště dat. Povolení těchto pravidel umožní, aby se místní prostředí Integration runtime připojovalo ke zdroji i ke jímka úspěšně. Povolte pravidla pro každé úložiště dat, které je součástí operace kopírování.

Pokud například chcete kopírovat z místního úložiště dat do jímky Azure SQL Database nebo jímky Azure SQL Data Warehouse, proveďte následující kroky:

1. Povolí odchozí komunikaci TCP na portu 1433 pro bránu firewall systému Windows i pro podnikovou bránu firewall.
2. Nakonfigurujte nastavení brány firewall služby Azure SQL Database tak, aby se do seznamu povolených IP adres přidala IP adresa počítače s místním prostředím Integration runtime.

> [!NOTE]
> Pokud brána firewall nepovoluje odchozí port 1433, místní prostředí Integration runtime nemůže získat přímý přístup k databázi SQL Azure. V takovém případě můžete použít [dvoufázové kopírování](copy-activity-performance.md) Azure SQL Database a Azure SQL Data Warehouse. V tomto scénáři budete pro přesun dat potřebovat jenom HTTPS (port 443).


## <a name="proxy-server-considerations"></a>Požadavky na proxy server
Pokud vaše firemní síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte prostředí Integration runtime v místním prostředí tak, aby používalo příslušné nastavení proxy serveru. Proxy server můžete nastavit během fáze prvotní registrace.

![Zadat proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Při nakonfigurovaném prostředí Integration runtime v místním prostředí používá proxy server pro připojení ke cloudové službě, zdroji nebo cíli (pomocí protokolu HTTP/HTTPS). Toto je výběr **odkazu na změnu** při počátečním nastavení. Zobrazí se dialogové okno nastavení proxy serveru.

![Nastavit proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Existují tři možnosti konfigurace:

- **Nepoužívat proxy server**: Místní prostředí Integration runtime explicitně nepoužívá žádný proxy server pro připojení ke cloudovým službám.
- **Použít systémový proxy server**: Místní prostředí Integration runtime používá nastavení proxy, které je nakonfigurováno v diahost. exe. config a diawp. exe. config. Pokud v souboru diahost. exe. config a diawp. exe. config není nakonfigurovaný žádný proxy server, místní prostředí Integration runtime se ke cloudové službě připojí přímo, aniž by se museli procházet proxy serverem.
- **Použít vlastní proxy**: Nakonfigurujte nastavení proxy serveru HTTP tak, aby se používalo pro místní prostředí Integration runtime, namísto použití konfigurací v diahost. exe. config a diawp. exe. config. **Adresa** a **port** jsou požadovány. **Uživatelské jméno** a **heslo** jsou nepovinné v závislosti na nastavení ověřování proxy serveru. Všechna nastavení jsou šifrovaná pomocí rozhraní Windows DPAPI v místním prostředí Integration runtime a uložená místně na počítači.

Po uložení aktualizovaných nastavení proxy serveru se služba Host prostředí Integration runtime automaticky restartuje.

Po úspěšné registraci místního prostředí Integration runtime, pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru, použijte Integration Runtime Configuration Manager.

1. Otevřete **Microsoft Integration Runtime Configuration Manager**.
2. Přepněte na kartu **Nastavení**.
3. Výběrem odkazu **změnit** v části **proxy HTTP** otevřete dialogové okno **nastavit proxy server http** .
4. Vyberte **Další**. Zobrazí se upozornění s dotazem na vaše oprávnění k uložení nastavení proxy serveru a opětovném spuštění hostitelské služby modulu runtime integrace.

Proxy server HTTP můžete zobrazit a aktualizovat pomocí nástroje Configuration Manager.

![Zobrazit proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Pokud nastavíte proxy server s ověřováním NTLM, spustí se služba hostitele modulu runtime integrace v rámci účtu domény. Pokud později změníte heslo pro účet domény, nezapomeňte aktualizovat nastavení konfigurace služby a odpovídajícím způsobem ho znovu spustit. Z tohoto důvodu doporučujeme použít vyhrazený doménový účet pro přístup k proxy server, která nevyžaduje, aby se heslo často aktualizovalo.

### <a name="configure-proxy-server-settings"></a>Konfigurace nastavení proxy server

Pokud vyberete nastavení **použít systém proxy** serveru pro proxy server http, místní prostředí Integration runtime používá nastavení proxy v souboru diahost. exe. config a diawp. exe. config. Pokud v souboru diahost. exe. config a diawp. exe. config není zadaný žádný proxy server, místní prostředí Integration runtime se ke cloudové službě připojuje přímo bez přechodu přes proxy server. Následující postup poskytuje pokyny k aktualizaci souboru diahost. exe. config:

1. V Průzkumníku souborů vytvořte zabezpečenou kopii složky C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config, která zálohuje původní soubor.
2. Otevřete program Notepad. exe spuštěný jako správce a otevřete textový soubor C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Najděte výchozí značku pro system.net, jak je znázorněno v následujícím kódu:

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

    V rámci značky proxy jsou povoleny další vlastnosti, které určují požadovaná nastavení `scriptLocation`. Viz syntaxe [elementu proxy (nastavení sítě)](https://msdn.microsoft.com/library/sa91de1e.aspx) .

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Uložte konfigurační soubor do původního umístění. Pak restartujte hostitelskou službu Integration runtime v místním prostředí, která změny převezme. 

   Chcete-li službu restartovat, použijte aplet služby z ovládacích panelů. Nebo z Integration Runtime Configuration Manager, vyberte tlačítko **Zastavit službu** a pak vyberte **Spustit službu**. 
   
   Pokud se služba nespustí, je pravděpodobnější, že se do konfiguračního souboru aplikace, který byl upraven, přidala nesprávná syntaxe značky XML.

> [!IMPORTANT]
> Nezapomeňte aktualizovat diahost. exe. config i diawp. exe. config.

Musíte se také ujistit, že Microsoft Azure je v seznamu povolených položek vaší společnosti. Seznam platných IP adres Microsoft Azure můžete stáhnout z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Možné příznaky pro bránu firewall a problémy s proxy server
Pokud dojde k chybám, které jsou podobné následujícím těm, je pravděpodobně způsobena nesprávnou konfigurací brány firewall nebo proxy server, která blokuje místní prostředí Integration runtime z připojení k Data Factory, aby se mohl sám ověřit. Pokud chcete zajistit správnou konfiguraci brány firewall a proxy server, přečtěte si předchozí část.

* Při pokusu o registraci místního prostředí Integration runtime se zobrazí následující chyba: "Nepovedlo se zaregistrovat tento uzel Integration Runtime! Potvrďte, že ověřovací klíč je platný a že je na tomto počítači spuštěná služba hostitele integrační služby.
* Když otevřete Integration Runtime Configuration Manager, zobrazí se stav **Odpojeno** nebo **připojení**. Při prohlížení protokolů událostí systému Windows v části **Prohlížeč událostí** >  > **protokolů aplikací a služeb** **Microsoft Integration runtime**se zobrazí chybové zprávy, jako je tato:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Povolení vzdáleného přístupu z intranetu  
Pokud použijete PowerShell k šifrování přihlašovacích údajů z jiného počítače (v síti), než ve kterém je nainstalovaný modul runtime integrace v místním prostředí, můžete povolit možnost **vzdálený přístup z intranetu** . Pokud spustíte PowerShell k šifrování přihlašovacích údajů ve stejném počítači, na kterém je nainstalovaný modul runtime integrace v místním prostředí, nemůžete povolit **vzdálený přístup z intranetu**.

Před přidáním dalšího uzlu pro vysokou dostupnost a škálovatelnost byste měli povolit **vzdálený přístup z intranetu** .  

Během instalace v místním prostředí Integration runtime (verze 3.3. xxxx. x novější) ve výchozím nastavení zakáže instalace místního prostředí Integration runtime **vzdálený přístup z intranetu** na počítači s integrací prostředí Integration runtime.

Pokud používáte bránu firewall jiného výrobce, můžete ručně otevřít port 8060 (nebo port konfigurovaný uživatelem). Pokud máte problém s bránou firewall při nastavování místního prostředí Integration runtime, zkuste použít následující příkaz pro instalaci místního prostředí Integration runtime bez konfigurace brány firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Pokud se rozhodnete neotevírat port 8060 na počítači prostředí Integration runtime v místním prostředí, použijte k nakonfigurování přihlašovacích údajů pro úložiště dat jiné mechanismy než aplikace s nastavením přihlašovacích údajů. Můžete například použít rutinu **New-AzDataFactoryV2LinkedServiceEncryptCredential** prostředí PowerShell.


## <a name="next-steps"></a>Další postup
Podrobné pokyny najdete v následujícím kurzu: [Kurz: Kopírování místních dat do cloudu](tutorial-hybrid-copy-powershell.md).
