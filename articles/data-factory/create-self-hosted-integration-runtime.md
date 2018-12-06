---
title: Vytvoření místního prostředí integration runtime ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit místní prostředí integration runtime v Azure Data Factory, která umožňuje přístup k úložišti dat v privátní síti datové továrny.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: f0040f7e84fefd745b3ca097a4808dc685dd5b72
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969477"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Vytvoření a konfigurace místní prostředí integration runtime
Prostředí integration runtime (IR) je výpočetní infrastruktura, která Azure Data Factory používá pro poskytují funkce integrace dat v různých síťových prostředích. Podrobnosti o prostředí IR najdete v tématu [přehled modulu runtime integrace](concepts-integration-runtime.md).

Místní prostředí integration runtime můžete spuštění aktivity kopírování mezi cloudovým úložištěm dat a úložiště dat v privátní síti a jeho odeslání aktivit transformace pro výpočetní prostředky v místní síti nebo virtuální síti Azure. Instalace modulu runtime integrace v místním prostředí vyžaduje na místní počítač nebo virtuální počítač (VM) v rámci privátní sítě.  

Tento dokument popisuje, jak můžete vytvořit a nakonfigurovat v místním prostředí IR.

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Podrobný postup instalace prostředí IR
1. Vytvořte místní prostředí Integration Runtime. Uživatelské rozhraní Azure Data Factory můžete použít pro tuto úlohu. Tady je příklad Powershellu:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Stáhněte si](https://www.microsoft.com/download/details.aspx?id=39717) a nainstalovat místní prostředí integration runtime v místním počítači.

3. Načíst ověřovací klíč a zaregistrujte modul runtime integrace v místním prostředí s klíčem. Tady je příklad Powershellu:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template-automation"></a>Nastavení místní prostředí IR na Virtuálním počítači Azure s použitím šablony Azure Resource Manageru (Automatizace)
Instalace v místním prostředí IR na virtuálním počítači Azure můžete automatizovat pomocí [tuto šablonu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Tato šablona poskytuje snadný způsob, jak máte plně funkční v místním prostředí IR uvnitř virtuální sítě Azure s funkcemi vysoké dostupnosti a škálovatelnosti (za předpokladu, můžete nastavit počet uzlů na 2 nebo vyšší).

## <a name="command-flow-and-data-flow"></a>Příkaz toku a toku dat
Při přesouvání dat mezi místní a cloudové aktivity místní prostředí integration runtime používá k přenosu dat z místních zdrojů dat do cloudu a naopak.

Tady je podrobný datový tok pro přehled kroků pro kopírování s místní prostředí IR:

![Podrobný přehled](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Vývojáři dat místní prostředí integration runtime v rámci služby Azure data factory vytvoří pomocí rutiny prostředí PowerShell. Na webu Azure portal v současné době nepodporuje tuto funkci.
2. Vývojáři dat vytvoří propojené služby pro do místního úložiště dat tak, že zadáte instancí modulu runtime integrace v místním prostředí, který se má používat pro připojení k úložišti dat. Jako součást nastavení propojené služby vývojář dat používá aplikace Správce přihlašovacích údajů (aktuálně není podporováno) pro nastavení typů ověřování a přihlašovací údaje. Aplikace Správce přihlašovacích údajů komunikuje s úložišti dat k testování připojení a místní prostředí integration runtime k uložení přihlašovacích údajů.
3. Uzel v místním prostředí integration runtime šifruje přihlašovací údaje s použitím Windows Data Protection Application Programming rozhraní (DPAPI) a uloží pověření místně. Více uzlů se nastavují pro vysokou dostupnost, přihlašovací údaje jsou další synchronizaci na jiných uzlech. Každý uzel šifruje přihlašovací údaje pomocí rozhraní DPAPI a ukládá je místně. Synchronizace přihlašovacích údajů je transparentní pro vývojáře, data a zařizuje služba v místním prostředí IR.    
4. Služba Data Factory komunikuje s modulem runtime integrace v místním prostředí pro plánování a Správa úloh prostřednictvím *řídicí kanál* , který používá sdílené fronty Azure Service Bus. Pokud úlohu aktivity musí být spuštěn, Data Factory zařadí do fronty požadavek spolu s žádné přihlašovací údaje (v případě přihlašovací údaje nejsou již uloženy v místním prostředí integration runtime). Místní prostředí integration runtime zahajuje úlohy po dotazování fronty.
5. Místní prostředí integration runtime kopíruje data z místního úložiště do cloudového úložiště, nebo naopak v závislosti na konfiguraci aktivitu kopírování v datovém kanálu. Pro tento krok místní prostředí integration runtime přímo komunikuje s služeb cloudového úložiště, jako je úložiště objektů Blob v Azure přes zabezpečený kanál (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Předpoklady pro použití v místním prostředí IR

- Jeden místní prostředí integration runtime je použít pro více zdrojů dat v místním. Jeden místní prostředí integration runtime je sdílet s jinou data factory ve stejném tenantovi Azure Active Directory. Další informace najdete v tématu [sdílení místního prostředí integration runtime](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Může mít pouze jednu instanci z místního prostředí integration runtime nainstalovaný na jednom počítači. Pokud máte dva datové továrny, které je potřeba přístup k místním zdrojům dat, budete muset nainstalovat místní prostředí integration runtime na dvou místních počítačů. Jinými slovy, místní prostředí integration runtime se váže na konkrétní datové továrny.
- Místní prostředí IR nemusí být ve stejném počítači jako zdroj dat. Však s místní prostředí integration runtime blíže ke zdroji dat snižuje čas potřebný pro místní prostředí integration runtime připojit ke zdroji dat. Doporučujeme nainstalovat místní prostředí integration runtime na počítač, který se liší od zdroje dat v místním hostiteli. Když místního prostředí integration runtime a zdroje dat jsou na různých počítačích, místní prostředí integration runtime není soutěží o prostředky se zdrojem dat.
- Na různých počítačích, které se připojují ke stejnému zdroji dat v místním může mít více modulů runtime integrace v místním prostředí. Například můžete mít dva moduly runtime integrace v místním prostředí, které slouží dva datové továrny, ale stejného zdroje dat v místním zaregistruje datové továrny.
- Pokud už máte nainstalovanou ve vašem počítači k obsluze scénáři Power BI bránu, nainstalujte samostatnou místní prostředí integration runtime pro službu Azure Data Factory na jiném počítači.
- Místní prostředí integration runtime musí použít pro podporu integrace dat v rámci virtuální sítě Azure.
- Zdroj dat považovat za zdroj dat místní, který je za bránou firewall, i když používáte Azure ExpressRoute. Můžete navázat připojení mezi službou a zdroj dat místní prostředí integration runtime.
- Musíte použít modul runtime integrace v místním prostředí i v případě, že je úložiště dat v cloudu na virtuálním počítači Azure IaaS.
- Úkoly se nemusí podařit v místním prostředí integration runtime, který je nainstalovaný na Windows serveru, na které kompatibilní se standardem FIPS je povolené šifrování. Chcete-li tento problém vyřešit, zakažte šifrování standardu FIPS na serveru. Pokud chcete zakázat šifrování standardu FIPS, změňte následující hodnotu registru z 1 (povolena) na hodnotu 0 (zakázáno): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Požadavky

- Jsou verze podporovaný operační systém Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2 a Windows serveru 2016. Instalace modulu runtime integrace v místním prostředí na řadiči domény není podporována.
- Rozhraní .NET framework 4.6.1 nebo novější je povinný. Pokud instalujete místní prostředí integration runtime na počítači s Windows 7, nainstalujte rozhraní .NET Framework 4.6.1 nebo novější. Zobrazit [rozhraní .NET Framework System Requirements](/dotnet/framework/get-started/system-requirements) podrobnosti.
- Doporučená konfigurace pro počítač místního prostředí integration runtime je aspoň 2 GHz, 4 jádra, 8 GB paměti RAM a 80GB disk.
- Pokud hostitelský počítač přejde do režimu spánku, neodpovídá na požadavky na data místní prostředí integration runtime. Nakonfigurujte plán odpovídající power počítače před instalací místní prostředí integration runtime. Pokud je počítač nakonfigurovaný do režimu hibernace, vyzve instalace modulu runtime integrace v místním prostředí zprávu.
- Musíte být správcem na počítači pro instalaci a konfiguraci místního prostředí integration runtime úspěšně.
- Spuštění aktivit kopírování provádělo na konkrétní frekvence. Využití prostředků (procesor, paměť) na počítači používá stejný vzor s ve špičce a doby nečinnosti. Využití prostředků také závisí do značné míry na množství dat, který se přesouvá. Právě probíhají úlohy s více kopií, uvidíte využití prostředků zvýší během špiček.

## <a name="installation-best-practices"></a>Osvědčené postupy instalace
Stáhněte si balíček MSI Instalační program z můžete nainstalovat místní prostředí integration runtime [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Zobrazit [přesun dat mezi místním prostředím a cloudem článku](tutorial-hybrid-copy-powershell.md) podrobné pokyny.

- Konfigurovat schéma napájení na hostitelském počítači pro místní prostředí integration runtime tak, aby počítač nepřejde do režimu spánku. Pokud hostitelský počítač přejde do režimu spánku, místní prostředí integration runtime přejde do režimu offline.
- Přihlašovací údaje související s místní prostředí integration runtime pravidelně zálohujte.

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Instalace a registrace v místním prostředí IR ze služby Stažení softwaru

1. Přejděte [stránku pro stažení modulu runtime integrace Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Vyberte **Stáhnout**, vyberte odpovídající verzi (**32-bit** nebo **64-bit**) a vyberte **Další**.
3. Spusťte instalační soubor MSI přímo, nebo ho uložit na pevný disk a spusťte jej.
4. Na **úvodní** stránky, vyberte jazyk a vyberte **Další**.
5. Přijměte licenční podmínky pro Software společnosti Microsoft a vyberte **Další**.
6. Vyberte **složky** nainstalovat modul runtime integrace v místním prostředí a vyberte **Další**.
7. Na **připraveno k instalaci** stránce **nainstalovat**.
8. Klikněte na tlačítko **Dokončit** pro dokončení instalace.
9. Získání klíče ověřování pomocí Azure Powershellu. Tady je příklad Powershellu pro načtení ověřovací klíč:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na **registrace prostředí Integration Runtime (v místním prostředí)** stránky Microsoft Integration Runtime Configuration Manageru spuštěn v počítači, proveďte následující kroky:

    a. Ověřovací klíč vložte do textového pole.

    b. Volitelně vyberte **zobrazit ověřovací klíč** zobrazíte text klíče.

    c. Vyberte **Zaregistrovat**.


## <a name="high-availability-and-scalability"></a>Vysoká dostupnost a škálovatelnost
Místní prostředí integration runtime můžou být spojené s více místních počítačů. Tyto počítače se označují jako uzly. Můžete mít až čtyři uzly, které jsou spojené s místní prostředí integration runtime. Výhody s více uzly (na místních počítačích s nainstalovanou bránu) pro logické brány jsou:
* Vyšší dostupnost modulu runtime integrace v místním prostředí tak, že je už jediným bodem selhání v integraci velké objemy dat řešení nebo cloudových dat pomocí Azure Data Factory, zajištění kontinuity podnikových procesů s až čtyři uzly.
* Vylepšili jsme výkon a propustnost během přesouvání dat mezi místním prostředím a cloudem datových úložišť. Získat další informace o [porovnání výkonu](copy-activity-performance.md).

Po instalaci softwaru místní prostředí integration runtime z můžete přidružit více uzlů [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Potom, zaregistrujte ho pomocí některé z ověřovací klíče získané z **New-AzureRmDataFactoryV2IntegrationRuntimeKey** rutiny, jak je popsáno v [kurzu](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Není nutné vytvořit nový modul runtime integrace v místním prostředí pro přidružení každý uzel. Můžete nainstalovat místní prostředí integration runtime na jiném počítači a zaregistrujte ho pomocí stejné ověřovací klíč. 

> [!NOTE]
> Abyste mohli přidat jiný uzel pro vysokou dostupnost a škálovatelnost, ujistěte se, že **vzdálený přístup k intranetu** na prvním uzlu je povolena možnost (**Správce konfigurace modulu Microsoft Integration Runtime**  >  **Nastavení** > **vzdálený přístup k intranetu**). 

### <a name="scale-considerations"></a>Důležité informace o škálování

#### <a name="scale-out"></a>Horizontální navýšení kapacity

Když je málo dostupné paměti na místní prostředí IR a využití CPU je vysoké, přidání nového uzlu pomáhá horizontální navýšení kapacity zatížení napříč počítači. Pokud aktivity selhávají, protože jste vypršení časového limitu nebo protože uzel v místním prostředí IR je offline, pomůže, pokud chcete přidat uzel do brány.

#### <a name="scale-up"></a>Vertikální navýšení kapacity

Pokud nejsou dostupné paměti a procesoru využívá dobře, ale spuštění souběžných úloh se blíží limitu, by měla vertikálně navýšit kapacitu zvýšením počtu souběžných úloh, které můžou běžet na uzlu. Můžete také vertikálně navýšit kapacitu, když aktivity jsou vypršení časového limitu, protože místní prostředí IR je přetížena. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacita pro uzel:  

![Zvýšení souběžných úloh, které můžou běžet na uzlu](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL

Tady jsou požadavky na certifikát TLS/SSL, který se používá k zabezpečení komunikace mezi integrace uzly modulu runtime:

- Certifikát musí být veřejně důvěryhodné X509 certifikát v3. Doporučujeme používat certifikáty vydané veřejnou (partner) certifikační autority (CA).
- Každý uzel integration runtime musí důvěřovat tomuto certifikátu.
- Alternativní název předmětu (SAN) certifikáty nedoporučujeme, protože se použijí jenom poslední položka po síti SAN a všechny ostatní se bude ignorovat kvůli aktuálním omezením. Například pokud máte síť SAN certifikát, jehož sítě SAN jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, tento certifikát můžete použít pouze v počítači, jehož plně kvalifikovaný název domény je  **NODE2.domain.contoso.com**.
- Certifikát podporuje všechny klíče velikost podporovaná ve Windows serveru 2012 R2 pro certifikáty SSL.
- Certifikáty, které používají klíče CNG nejsou podporovány.  

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Sdílení místního prostředí integration runtime s více objekty pro vytváření dat

Můžete znovu použít existující infrastrukturu modulu runtime integrace v místním prostředí, které jste už nastavili ve službě data factory. To vám umožní vytvořit *propojená místní prostředí integration runtime* v různých datových pomocí odkazu na existující objekt pro vytváření v místním prostředí IR (sdílené).

Sdílet místní prostředí integration runtime pomocí Powershellu, najdete v článku [vytvořit sdílené místní prostředí integration runtime ve službě Azure Data Factory pomocí Powershellu](create-shared-self-hosted-integration-runtime-powershell.md).

Pro zavedení dvanácti minutu a ukázku této funkce z následujícího videa:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologie

- **Sdílené reakcí na Incidenty**: původní v místním prostředí IR, na kterém běží na fyzickou infrastrukturu.  
- **Propojené prostředí IR**: The reakcí na Incidenty, která odkazuje na jiné sdílené IR. Toto je logický IR a využívá infrastrukturu jinou místní prostředí IR (sdílené).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Postup vysoké úrovně pro vytvoření propojené prostředí IR v místním prostředí

1. Místní prostředí IR ke sdílení udělte oprávnění do služby data factory, ve kterém chcete vytvořit propojené IR. 

   ![Tlačítko pro udělování oprávnění na kartě Sdílení](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Výběry pro přiřazení oprávnění](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Poznamenejte si ID prostředku v místním prostředí IR umožňuje sdílení.

   ![Umístění ID prostředku](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. Ve službě data factory, ke kterému byla udělena oprávnění vytvořit nový místní prostředí IR (odkaz) a zadejte ID prostředku.

   ![Tlačítko pro vytvoření propojené místní prostředí integration runtime](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Pole pro název a ID prostředku](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Monitorování 

- **Sdílené reakcí na Incidenty**

  ![Výběry pro vyhledání sdílené prostředí IR](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Karta pro monitorování](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Propojené prostředí IR**

  ![Výběry pro vyhledání propojených integration runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Karta pro monitorování](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Známá omezení sdílení v místním prostředí IR

* Musí mít datové továrny, ve kterém se vytvoří propojené prostředí IR [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Ve výchozím nastavení objekty pro vytváření dat vytvořen na webu Azure Portal nebo rutiny prostředí PowerShell mají MSI vytvářejí implicitně. Ale když se vytvoří objekt pro vytváření dat prostřednictvím šablony Azure Resource Manageru nebo SDK, **Identity** vlastnost musí být explicitně nastaveno na Ujistěte se, že Azure Resource Manageru vytvoří objekt pro vytváření dat, která obsahuje soubor MSI. 

* Azure Data Factory .NET SDK, která podporuje tuto funkci je verze 1.1.0 nebo novější.

* Verze prostředí Azure PowerShell, který podporuje tuto funkci je 6.6.0 nebo novější (AzureRM.DataFactoryV2 0.5.7 nebo novější).

* Udělení oprávnění, uživatel musí roli vlastníka nebo děděných rolí vlastník ve službě data factory, kde existuje sdílený reakcí na Incidenty.

* Sdílení funkce funguje pouze pro datové továrny ve stejném tenantovi Azure Active Directory.

* Pro Active Directory [uživatele typu Host](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews), funkce vyhledávání (výpisu všech datových továren s použitím hledaná klíčová slova) v uživatelském rozhraní [nefunguje](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Ale za předpokladu, uživatel typu Host je vlastníkem služby data factory, IR bez vyhledávacích funkcí, můžou sdílet přímo zadáním MSI služby data factory, pomocí kterého potřeba sdílet v prostředí IR **přiřadit oprávnění** textového pole a Výběr **přidat** v Uživatelském rozhraní Azure Data Factory. 

  > [!NOTE]
  > Tato funkce je dostupná jenom v Azure Data Factory V2. 

## <a name="notification-area-icons-and-notifications"></a>Ikony oznamovací oblasti a oznámení

Pokud přesunete ukazatel myši nad ikonu a zprávy v oznamovací oblasti, můžete najít podrobnosti o stavu z místního prostředí integration runtime.

![Oznámení v oznamovací oblasti](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Porty a brány firewall
Existují dvě brány firewall, které byste měli zvážit: *podnikovou bránu firewall* běžící na střed směrovače organizace a *brány Windows firewall* nakonfigurovaný jako démon na místním počítači kde místní prostředí integration runtime se nainstaluje.

![Brána firewall](media/create-self-hosted-integration-runtime/firewall.png)

Na *podnikovou bránu firewall* úroveň, budete muset nakonfigurovat následující domény a odchozích portech:

Názvy domén | Porty | Popis
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Slouží ke komunikaci s back endovým datům služba pro přesun
*.core.windows.net | 443 | Používá pro fázované kopírování prostřednictvím služby Azure Blob storage (Pokud je nakonfigurovaná)
*.frontend.clouddatahub.net | 443 | Slouží ke komunikaci s back endovým datům služba pro přesun
download.microsoft.com | 443 | Použít pro stažení aktualizace

Na *brány Windows firewall* úroveň (úrovni počítače), tyto porty pro odchozí spojení jsou obvykle povolené. Pokud ne, můžete nakonfigurovat domény a porty odpovídajícím způsobem na počítači místní prostředí integration runtime.

> [!NOTE]
> Založené na zdroji a jímkami, bude pravděpodobně k další domény seznamu povolených IP adres a odchozí porty v podnikové brány firewall nebo brána Windows firewall.
>
> Pro některé databáze v cloudu (například Azure SQL Database a Azure Data Lake) může být nutné do seznamu povolených IP adres počítačů místní prostředí integration runtime na jejich konfiguraci brány firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopírování dat ze zdroje do jímky
Ujistěte se, že pravidla brány firewall jsou povolené vhodným způsobem v podnikové brány firewall, Windows firewall na počítači pro místní prostředí integration runtime a samotné úložiště dat. Když se tato pravidla povolí místní prostředí integration runtime se připojit k oba zdroje a jímky úspěšně. Povolte pravidla pro každé úložiště dat, která je zahrnuta v operaci kopírování.

Například kopírování z do místního úložiště dat jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky, proveďte následující kroky:

1. Povolí odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a podnikovou bránu firewall.
2. Konfigurace nastavení brány firewall služby Azure SQL database pro přidání do seznamu povolených IP adres IP adresu počítače místní prostředí integration runtime.

> [!NOTE]
> Pokud brána firewall neumožňuje odchozí port 1433, místní prostředí integration runtime nelze přímý přístup k databázi Azure SQL. V takovém případě můžete použít [fázovaného kopírování](copy-activity-performance.md) do Azure SQL Database a Azure SQL Data Warehouse. V tomto scénáři by vyžadují pouze HTTPS (port 443) pro přesun dat.


## <a name="proxy-server-considerations"></a>Důležité informace o proxy serveru
Pokud vaše podnikové síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte místní prostředí integration runtime používat příslušná nastavení proxy serveru. Během fáze počáteční registraci můžete nastavit proxy server.

![Zadejte proxy server](media/create-self-hosted-integration-runtime/specify-proxy.png)

Místní prostředí integration runtime používá proxy server pro připojení ke cloudové službě. Vyberte **odkaz Změnit** při počátečním nastavení. Zobrazí dialogové okno nastavení proxy serveru.

![Nastavení proxy serveru](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Existují tři možnosti konfigurace:

- **Nepoužívat proxy server**: místní prostředí integration runtime připojit ke cloudovým službám explicitně nepoužívá žádné proxy.
- **Použít systémový proxy server**: místní prostředí integration runtime používá proxy server, který je nastavení nakonfigurované v diahost.exe.config a diawp.exe.config. Pokud žádný proxy server je nakonfigurován diahost.exe.config a diawp.exe.config, místní prostředí integration runtime připojí ke cloudové službě přímo bez proxy serveru.
- **Použít vlastní proxy server**: Konfigurace nastavení pro místní prostředí integration runtime, namísto použití konfigurace v diahost.exe.config a diawp.exe.config proxy HTTP. **Adresa** a **Port** jsou povinné. **Uživatelské jméno** a **heslo** jsou volitelné na základě nastavení ověřování váš proxy server. Všechna nastavení jsou šifrované pomocí rozhraní Windows DPAPI na místní prostředí integration runtime a ukládají místně na počítači.

Hostitelská služba modulu integration runtime se restartuje automaticky po uložení aktualizované nastavení proxy serveru.

Po místní prostředí integration runtime byl úspěšně zaregistrován, pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru, použijte Správce konfigurace modulu Integration Runtime.

1. Otevřít **Správce konfigurace modulu Microsoft Integration Runtime**.
2. Přepněte na kartu **Nastavení**.
3. Vyberte **změnu** odkaz v **proxy server HTTP** části pro otevření **nastavit proxy server HTTP** dialogové okno.
4. Vyberte **Další**. Zobrazit upozornění, která požádá o vaše oprávnění k uložení nastavení proxy serveru a restartovat hostitelskou službu modulu integration runtime.

Můžete zobrazit a aktualizovat proxy server HTTP s použitím nástroje Configuration Manager.

![Zobrazení proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Pokud nastavení proxy serveru pomocí ověřování NTLM hostitelská služba modulu integration runtime běží pod účtem domény. Pokud změníte heslo pro účet domény, později, nezapomeňte aktualizovat nastavení konfigurace pro službu a restartujte ji odpovídajícím způsobem. Kvůli tomuto požadavku, doporučujeme použít vyhrazený doménový účet pro přístup k proxy serveru, který nevyžaduje často aktualizujte heslo.

### <a name="configure-proxy-server-settings"></a>Konfigurace nastavení proxy serveru

Pokud vyberete **použít systémový proxy server** nastavení pro proxy server HTTP, místní prostředí integration runtime používá nastavení proxy v diahost.exe.config a diawp.exe.config. Pokud v diahost.exe.config a diawp.exe.config není zadán žádný proxy server, místní prostředí integration runtime připojí ke cloudové službě přímo bez proxy serveru. Následující postup obsahuje pokyny pro aktualizaci diahost.exe.config souboru:

1. V Průzkumníku souborů vytvořte kopii bezpečné C:\Program Files\Microsoft integrace Runtime\3.0\Shared\diahost.exe.config zálohovat původní soubor.
2. Otevřete Notepad.exe jako správce a otevřete textový soubor C:\Program Files\Microsoft integrace Runtime\3.0\Shared\diahost.exe.config. Najdete výchozí značka pro system.net, jak je znázorněno v následujícím kódu:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Poté můžete přidat podrobnosti o proxy serveru, jak je znázorněno v následujícím příkladu:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Další vlastnosti jsou povolené uvnitř značky proxy k určení požadovaných nastavení, jako jsou `scriptLocation`. Zobrazit [proxy – Element (nastavení sítě)](https://msdn.microsoft.com/library/sa91de1e.aspx) syntaxi.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Uložte konfigurační soubor v původním umístění. Restartujte místní prostředí integration runtime služby hostitele, která převezme tyto změny. 

   Restartujte službu, pomocí apletu služby v Ovládacích panelech. Nebo vyberte ze Správce konfigurace modulu Runtime integrace **zastavit službu** tlačítko a pak vyberte **spustit službu**. 
   
   Pokud služba nespustí, je pravděpodobné, nesprávná syntaxe značky XML byl přidán v konfiguračním souboru aplikace, která byla upravena.

> [!IMPORTANT]
> Nezapomeňte aktualizovat diahost.exe.config a diawp.exe.config.

Musíte také zajistit, že Microsoft Azure je v seznamu povolených IP adres vaší společnosti. Můžete si stáhnout seznam platných adres IP adres Microsoft Azure z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Je to možné příznaky u problémů souvisejících se server brány firewall a proxy
Pokud narazíte na chyby podobné následující dotazy, je pravděpodobně v důsledku nesprávné konfigurace brány firewall nebo proxy serveru, který blokuje místní prostředí integration runtime v připojení ke službě Data Factory ke svému ověření. Ujistěte se, že jsou správně nakonfigurované brány firewall a proxy server, naleznete v předchozí části.

* Při pokusu o registraci místního prostředí integration runtime se zobrazí následující chybová zpráva: "se nepodařilo registrovat tento uzel Integration Runtime. Zkontrolujte, jestli je platný ověřovací klíč a služba pro integraci na tomto počítači je spuštěna služba hostitele."
* Když otevřete Správce konfigurace modulu Integration Runtime se zobrazí stav **odpojeno** nebo **připojení**. Když zobrazujete protokoly událostí Windows, v části **Prohlížeč událostí** > **protokoly aplikací a služeb** > **prostředí Microsoft Integration Runtime**, Zobrazí se chybové zprávy, jako je tato:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Povolení vzdáleného přístupu z intranetu  
Pokud používáte PowerShell nebo v aplikaci správce přihlašovacích údajů pro šifrování přihlašovacích údajů z jiného počítače (v síti), než kde je nainstalován modul runtime integrace v místním prostředí, můžete povolit **vzdálený přístup z intranetu**možnost. Při spuštění PowerShell nebo aplikace Správce přihlašovacích údajů k šifrování přihlašovacích údajů na stejném počítači, kde je nainstalován modul runtime integrace v místním prostředí, nelze povolit **vzdálený přístup z intranetu**.

Měli byste povolit **vzdálený přístup z intranetu** předtím, než přidáte další uzel pro vysokou dostupnost a škálovatelnost.  

Během místní prostředí integration runtime instalace (verze 3.3.xxxx.x později), ve výchozím nastavení instalace prostředí integration runtime zakáže **vzdálený přístup z intranetu** na počítači s modulem runtime integrace v místním prostředí.

Pokud používáte bránu firewall jiného dodavatele, můžete ručně otevřete port 8060 (nebo uživatel nakonfigurovaný port). Pokud máte brány firewall na problém při nastavování místní prostředí integration runtime, zkuste použít následující příkaz k instalaci místní prostředí integration runtime bez konfigurace brány firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> Aplikace Správce přihlašovacích údajů ještě není k dispozici pro šifrování přihlašovacích údajů v Azure Data Factory V2.  

Pokud zvolíte ne pro otevření portu 8060 na počítači s modulem runtime integrace v místním prostředí, použijte mechanismy než aplikace nastavení přihlašovacích údajů nakonfigurovat přihlašovací údaje úložiště dat. Například můžete použít **New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** rutiny Powershellu.


## <a name="next-steps"></a>Další postup
Projděte si následující kurz pro podrobné pokyny: [kurz: kopírování místních dat do cloudu](tutorial-hybrid-copy-powershell.md).
