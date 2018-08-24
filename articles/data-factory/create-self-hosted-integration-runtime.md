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
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 863cda349ca951bee1c43f09eefd364645de9dbd
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746797"
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Jak vytvořit a nakonfigurovat modul Integration Runtime
Integration Runtime (IR) je výpočetní infrastruktura, službou Azure Data Factory používá k poskytování možnosti integrace dat v různých síťových prostředích. Podrobnosti o prostředí IR najdete v tématu [přehled modulu Runtime integrace](concepts-integration-runtime.md).

Místní prostředí integration runtime je schopen spuštění aktivity kopírování mezi daty cloudové úložiště a úložiště dat v privátní síti a odeslání aktivit transformace výpočetní prostředky na místní nebo Azure Virtual Network. Místní prostředí integration runtime musí nainstalujte na místní počítač nebo virtuální počítač v rámci privátní sítě.  

Toto téma představuje, jak můžete vytvořit a nakonfigurovat místním IR.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Základní kroky pro instalaci místní prostředí IR
1. Vytvoření místního prostředí integration runtime. Uživatelské rozhraní ADF můžete použít k vytváření v místním prostředí IR. Tady je příklad Powershellu:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2. Stáhněte a nainstalujte místní prostředí integration runtime (v místním počítači).
3. Načíst ověřovací klíč a registrace místního prostředí integration runtime s klíčem. Tady je příklad Powershellu:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="setting-up-self-hosted-ir-on-azure-vm-using-azure-resource-manager-template-automatation"></a>Nastavení místní prostředí IR na virtuálním počítači Azure pomocí šablony Azure Resource Manageru (automatation)
Můžete automatizovat instalace v místním prostředí IR na virtuálním počítači Azure pomocí [tuto šablonu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). To poskytuje snadný způsob, jak máte plně funkční místní prostředí IR uvnitř virtuální sítě Azure s vysokou Avalaibility a škálovatelnost (za předpokladu, můžete nastavit počet uzlů na 2 nebo vyšší).

## <a name="command-flow-and-data-flow"></a>Příkaz toku a toku dat
Když přesouváte data mezi místním prostředím a cloudem, aktivita místní prostředí integration runtime používá k přenosu dat z místního zdroje dat do cloudu a naopak.

Tady je podrobný datový tok pro přehled kroků pro kopírování s místní prostředí IR:

![Podrobný přehled](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Vývojáři dat vytvoří místní prostředí integration runtime v rámci služby Azure data factory pomocí rutiny prostředí PowerShell. Na webu Azure portal v současné době nepodporuje tuto funkci.
2. Data developer vytvoří propojené služby pro místnímu úložišti dat zadáním instanci vlastním hostováním integrace modulu runtime, která měla použít pro připojení k úložištím dat. Jako součást nastavení propojené služby, vývojáři dat používá aplikace "správce přihlašovacích údajů" (v současné době není podporováno) pro nastavení typů ověřování a přihlašovací údaje. Dialogové okno přihlašovacích údajů správce aplikace komunikuje s úložišti dat k testování připojení a místní prostředí integration runtime k uložení přihlašovacích údajů.
   - Uzel v místním prostředí integration runtime šifruje přihlašovací údaje, které pomocí Windows Data Protection Application Programming rozhraní (API) a uloží ho místně. Více uzlů se nastavují pro vysokou dostupnost, přihlašovací údaje jsou další synchronizaci na jiných uzlech. Každý uzel ji zašifruje pomocí rozhraní DPAPI a uloží ho místně. Synchronizace přihlašovacích údajů je transparentní pro vývojáře, data a zařizuje služba v místním prostředí IR.    
   - Služba data Factory komunikuje s modulem runtime integrace v místním prostředí pro plánování a Správa úloh prostřednictvím **řídicí kanál** , která používá fronty Service bus sdílených služeb Azure. Pokud úlohu aktivity musí být spuštěn, Data Factory zařadí do fronty požadavek spolu s žádné přihlašovací údaje (v případě přihlašovací údaje nejsou již uloženy v místním prostředí integration runtime). Místní prostředí integration runtime zahajuje úlohy po dotazování fronty.
   - Místní prostředí integration runtime kopíruje data z místního úložiště do cloudového úložiště, nebo naopak v závislosti na konfiguraci aktivitu kopírování v datovém kanálu. Pro tento krok místní prostředí integration runtime přímo komunikuje se službou úložiště cloudových služeb, jako je Azure Blob Storage přes zabezpečený kanál (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Předpoklady pro použití v místním prostředí IR

- Jeden místní prostředí integration runtime je použít pro více zdrojů dat v místním. Nicméně **jeden místní prostředí integration runtime se váže na jenom jeden Azure data factory** a nemůže je sdílet s jinou datovou továrnu.
- Můžete mít **pouze jednu instanci z místního prostředí integration runtime** nainstalována na jednom počítači. Předpokládejme, máte dva datové továrny, které potřebují přístup k místním zdrojům dat, budete muset nainstalovat místní prostředí integration runtime na dvou místních počítačů. Jinými slovy, místní prostředí integration runtime se váže na konkrétní datové továrny
- **Místní prostředí IR nemusí být ve stejném počítači jako zdroj dat**. Však s místním prostředí integration runtime blíže ke zdroji dat snižuje čas potřebný pro místní prostředí integration runtime připojit ke zdroji dat. Doporučujeme nainstalovat místní prostředí integration runtime na počítač, který se liší od zdroje dat v místním hostiteli. Když místního prostředí integration runtime a zdroje dat jsou na různých počítačích, místní prostředí integration runtime není soutěží o prostředky se zdrojem dat.
- Můžete mít **různými moduly runtime integrace v místním prostředí na různých počítačích připojení ke stejnému zdroji dat v místním**. Například může mít dvě místní prostředí integration runtime obsluhuje dva datové továrny, ale stejného zdroje dat v místním zaregistruje datové továrny.
- Pokud už máte nainstalovanou na váš počítač obsluhující bránu **Power BI** scénář, nainstalujte **samostatný místní prostředí integration runtime služby Azure Data Factory** na jiném počítači.
- Místní prostředí integration runtime musí použít pro podporu integrace dat v rámci virtuální sítě Azure.
- Považovat za zdroj dat místní, (který je za bránou firewall) zdroj dat i při použití **ExpressRoute**. Můžete navázat připojení mezi službou a zdroj dat místní prostředí integration runtime.
- Je nutné použít modul runtime integrace v místním prostředí i v případě, že je úložiště dat v cloudu **virtuálním počítači Azure IaaS**.
- Úkoly mohou selhat v modul Integration Runtime nainstalovaný v systému Windows Server, na které kompatibilní se standardem FIPS je povolené šifrování. Chcete-li tento problém obejít, zakažte šifrování standardu FIPS na serveru. Pokud chcete zakázat šifrování standardu FIPS, změňte následující hodnotu registru z 1 (povolena) na hodnotu 0 (zakázáno): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Požadavky

- Podporované **operačního systému** jsou verze Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. Instalace prostředí integration runtime na **řadič domény není podporován**.
- **Rozhraní .NET framework 4.6.1 nebo vyšší** je povinný. Pokud instalujete místní prostředí integration runtime na počítači s Windows 7, nainstalujte rozhraní .NET Framework 4.6.1 nebo novější. Zobrazit [rozhraní .NET Framework System Requirements](/dotnet/framework/get-started/system-requirements) podrobnosti.
- Doporučené **konfigurace** pro místní prostředí integration runtime je počítač minimálně 2 GHz, 4 jádra, 8 GB paměti RAM a 80 GB disk.
- Pokud hostitelský počítač přejde do režimu spánku, neodpovídá na požadavky na data místní prostředí integration runtime. Před instalací místní prostředí integration runtime proto nakonfigurujte plán služby odpovídající power v počítači. Pokud je počítač nakonfigurovaný do režimu hibernace, vyzve instalace modulu runtime integrace v místním prostředí zprávu.
- Musíte být správcem na počítači pro instalaci a konfiguraci místního prostředí integration runtime úspěšně.
- Při spuštění aktivity kopírování se provádělo na konkrétní frekvence, využití prostředků (procesor, paměť) na počítači také používá stejný vzor s ve špičce a doby nečinnosti. Využití prostředků také závisí do značné míry na množství dat, který se přesouvá. Právě probíhají úlohy s více kopií, uvidíte využití prostředků zvýší během špiček.

## <a name="installation-best-practices"></a>Osvědčené postupy instalace
Místní prostředí integration runtime může být instalován stahování balíčku MSI Instalační program z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Zobrazit [přesun dat mezi místním prostředím a cloudem článku](tutorial-hybrid-copy-powershell.md) podrobné pokyny.

- Konfigurovat schéma napájení na hostitelském počítači pro místní prostředí integration runtime tak, aby počítač nepřejde do režimu spánku. Pokud hostitelský počítač přejde do režimu spánku, místní prostředí integration runtime se změní v režimu offline.
- Přihlašovací údaje související s místní prostředí integration runtime pravidelně zálohujte.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Instalace a registrace místní prostředí IR ze služby Stažení softwaru

1. Přejděte do [stránce pro stahování Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klikněte na tlačítko **Stáhnout**, vyberte odpovídající verzi (**32-bit** vs. **64-bit**) a klikněte na tlačítko **Další**.
3. Spustit **MSI** přímo nebo uložit na pevný disk a spusťte.
4. Na **úvodní** stránce **jazyk** klikněte na tlačítko **Další**.
5. **Přijměte** licenční smlouvu a klikněte na tlačítko **Další**.
6. Vyberte **složky** nainstalovat místní prostředí integration runtime a klikněte na tlačítko **Další**.
7. Na **připraveno k instalaci** klikněte na **nainstalovat**.
8. Klikněte na tlačítko **Dokončit** pro dokončení instalace.
9. Získání klíče ověřování pomocí Azure Powershellu. Příklad Powershellu pro načtení ověřovací klíč:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na **registrace prostředí Integration Runtime (v místním prostředí)** stránky Microsoft Integration Runtime Configuration Manageru spuštěn v počítači, proveďte následující kroky:
    1. Vložit **ověřovací klíč** v části textu.
    2. Případně můžete kliknout **zobrazit ověřovací klíč** zobrazíte text klíče.
    3. Klikněte na tlačítko **zaregistrovat**.


## <a name="high-availability-and-scalability"></a>Vysoká dostupnost a škálovatelnost
Modul Integration Runtime může být přidružená k více místních počítačů. Tyto počítače se označují jako uzly. Můžete mít až čtyři uzly, které jsou přidružené k modul Integration Runtime. Výhody s více uzly (na místních počítačích s nainstalovanou bránu) pro logické brány jsou:
1. Vyšší dostupnost modul Integration Runtime tak, že již není jediným bodem selhání v integraci velké objemy dat řešení nebo cloudových dat pomocí Azure Data Factory, zajištění kontinuity podnikových procesů s až 4 uzly.
2. Vylepšili jsme výkon a propustnost během přesouvání dat mezi místním prostředím a cloudem datových úložišť. Získat další informace o [porovnání výkonu](copy-activity-performance.md).

Jednoduše instalací softwaru modul Integration Runtime z můžete přidružit více uzlů [centra stahování](https://www.microsoft.com/download/details.aspx?id=39717) a registrováním buď ověřovací klíče získané z Nové AzureRmDataFactoryV2IntegrationRuntimeKey rutiny, jak je popsáno v [kurz](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Není potřeba vytvořit nový modul Integration Runtime pro přidružení každý uzel. Můžete nainstalovat místní prostředí integration runtime na jiném počítači a zaregistrujte ho pomocí stejné ověřovací klíč. 

> [!NOTE]
> Před přidáním jiný uzel pro **vysokou dostupnost a škálovatelnost**, ujistěte se prosím **"Vzdálený přístup k intranetu"** je možnost **povolené** uzlu 1 (Microsoft Správce konfigurace modulu Integration Runtime -> Nastavení -> vzdáleného přístupu k intranetu). 

### <a name="scale-considerations"></a>Důležité informace o škálování

#### <a name="scale-out"></a>Horizontální navýšení kapacity

Když **dochází dostupná paměť na místní prostředí IR** a **využití procesoru je Vysoká**, přidání nového uzlu pomáhá horizontální navýšení kapacity zatížení napříč počítači. Pokud aktivity se nepovedlo kvůli vypršení časového limitu nebo v místním prostředí IR uzel je offline, pomůže, pokud chcete přidat uzel do brány.

#### <a name="scale-up"></a>Vertikální navýšení kapacity

Pokud nejsou dostupné paměti a procesoru využívá dobře, ale spuštění souběžných úloh se blíží limitu, by měla vertikálně navýšit kapacitu zvýšením počtu souběžných úloh, které můžou běžet na uzlu. Můžete také vertikálně navýšit kapacitu, když aktivity jsou vypršení časového limitu, protože místní prostředí IR je přetížena. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacita pro uzel.  

![](media\create-self-hosted-integration-runtime\scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL

Tady jsou požadavky na certifikát TLS/SSL, který se používá k zabezpečení komunikace mezi integrace uzly modulu runtime:

- Certifikát musí být veřejně důvěryhodné X509 certifikát v3. Doporučujeme používat certifikáty vydané veřejnou (třetí strany) certifikační autoritou (CA).
- Každý uzel integration runtime musí důvěřovat tomuto certifikátu.
- Zástupné certifikáty jsou podporovány. Pokud je váš plně kvalifikovaný název domény **node1.domain.contoso.com**, můžete použít ***. domain.contoso.com** jako název subjektu certifikátu.
- Certifikátů SAN se nedoporučuje, protože se použijí jenom poslední položka alternativní názvy subjektů a všechny ostatní se bude ignorovat kvůli aktuálním omezením. Například máte certifikát SAN jehož SAN jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, tento certifikát můžete použít jenom na počítači, jehož plně kvalifikovaný název domény je **node2.domain.contoso.com**.
- Podporuje všechny klíče velikost podporovaná ve Windows serveru 2012 R2 pro certifikáty SSL.
- Certifikátů CNG pomocí klíče nejsou podporovány.  

## <a name="sharing-the-self-hosted-integration-runtime-ir-with-multiple-data-factories"></a>Sdílení v místním prostředí Integration Runtime (IR) s více objekty pro vytváření dat

Můžete využít stávající infrastrukturu modulu runtime integrace v místním prostředí, že už můžete mít nastavení v datové továrně. Díky tomu můžete vytvořit **propojená místní prostředí integration runtime** v různých datových pomocí odkazu na již existující objekt pro vytváření v místním prostředí IR (sdílené).

#### <a name="terminologies"></a>**Terminologie**

- **Sdílené reakcí na Incidenty** – původní v místním prostředí IR, který je spuštěn na fyzickou infrastrukturu.  
- **Propojené prostředí IR** – The IR, což odkazuje na jiné sdílené IR. Toto je logický IR a využívá infrastrukturu jinou místní prostředí IR (sdílené).

#### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Vysoká úroveň kroky pro vytvoření propojené místní prostředí IR

V místním prostředí IR sdílet,

1. Udělit oprávnění k Data Factory, ve kterém chcete vytvořit propojené IR. 

   ![](media\create-self-hosted-integration-runtime\grant-permissions-IR-sharing.png)

2. Poznámka: **ID prostředku** v místním prostředí IR umožňuje sdílení.

   ![](media\create-self-hosted-integration-runtime\4_ResourceID_self-hostedIR.png)

Ve službě Data Factory, ke kterému byla udělena oprávnění,

3. Vytvořit nový místní prostředí IR (odkaz) a zadejte výše **ID prostředku**

   ![](media\create-self-hosted-integration-runtime\6_create-linkedIR_2.png)

   ![](media\create-self-hosted-integration-runtime\6_create-linkedIR_3.png)

#### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Známá omezení sdílení v místním prostředí IR

1. Výchozí počet propojených reakcí na Incidenty, které je možné vytvořit v rámci jedné místní prostředí IR je **20**. Pokud potřebujete více pak kontaktujte podporu. 

2. Data factory, ve kterém se má vytvořit propojené prostředí IR musí být soubor MSI ([se identita spravované služby](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)). Ve výchozím nastavení vytvoření datové továrny v portálu Ibiza nebo rutiny prostředí PowerShell bude mít MSI vytvářejí implicitně. Nicméně v některých případech při vytvoření datové továrny pomocí šablony správce prostředků různého Azure nebo SDK "**Identity**" **musí být nastavena vlastnost** explicitně zajistit správce prostředků různého Azure vytvoří objekt pro vytváření dat obsahující soubor MSI. 

3. Verze v místním prostředí IR musí být roven nebo větší než 3.8.xxxx.xx. Prosím [stáhnout nejnovější verzi](https://www.microsoft.com/download/details.aspx?id=39717) v místním prostředí IR

4. Data factory, ve kterém se má vytvořit propojené prostředí IR musí být soubor MSI ([se identita spravované služby](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)). Ve výchozím nastavení, bude mít datových továren v portálu Ibiza nebo rutiny prostředí PowerShell vytvoří MSI ([se identita spravované služby](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)).
vytvářejí implicitně, ale objekty pro vytváření dat vytvořen pomocí šablony Azure Resource Manageru (ARM) nebo sady SDK vyžaduje nastavení vlastnosti "Identity" k zajištění, že se vytvoří soubor MSI.

5. Sady ADF .net SDK, které podporují tuto funkci je verze > = 1.1.0

6. Prostředí Azure PowerShell, které podporují tuto funkci je verze > = 6.6.0 (AzureRM.DataFactoryV2 > = 0.5.7)

7. Udělení oprávnění, uživatel bude vyžadovat "Vlastník" nebo zděděná "vlastník" role ve službě Data Factory, kde existuje IR sdílené. 

  > [!NOTE]
  > Tato funkce je dostupná pouze ve službě Azure Data Factory verze 2 

## <a name="system-tray-icons-notifications"></a>Systémové ikony oznamovací oblasti nebo oznámení

Přesunutí kurzoru myši systému na hlavním panelu ikonu a oznámení najdete podrobnosti o stavu z místního prostředí integration runtime.

![Upozornění na hlavním panelu systému](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Porty a brány firewall
Existují dvě brány firewall, je potřeba zvážit: **podnikovou bránu firewall** běžící na střed směrovače organizace, a **brány Windows firewall** nakonfigurovaný jako démon na místním počítači kde místní prostředí integration runtime se nainstaluje.

![Brána firewall](media\create-self-hosted-integration-runtime\firewall.png)

Na **podnikovou bránu firewall** úrovně, třeba konfiguraci následujících domén a odchozích portech:

Názvy domén | Porty | Popis
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Slouží ke komunikaci s back-end služba pro přesun dat
*.core.windows.net | 443 | Použít pro kopírování připravený pomocí objektů Blob v Azure (Pokud je nakonfigurovaná)
*.frontend.clouddatahub.net | 443 | Slouží ke komunikaci s back-end služba pro přesun dat
download.microsoft.com | 443 | Použít pro stažení aktualizace

Na **brány Windows firewall** úroveň (úrovni počítače), tyto porty pro odchozí spojení jsou obvykle povolené. Pokud ne, můžete nakonfigurovat domény a porty odpovídajícím způsobem v místním prostředí integration runtime počítače.

> [!NOTE]
> Podle zdroje / jímky, bude pravděpodobně nutné další domény seznamu povolených IP adres a odchozí porty v bráně firewall podnikové/Windows.
>
> U některých cloudových databází (například: Azure SQL Database, Azure Data Lake, atd.), budete muset seznamu povolených IP adres IP adresu počítače místní prostředí integration runtime na jejich konfiguraci brány firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopírování dat ze zdroje do jímky
Ujistěte se, že pravidla brány firewall jsou povolené vhodným způsobem v podnikové brány firewall, brány Windows firewall na počítači pro místní prostředí integration runtime a samotné úložiště dat. Když se tato pravidla povolí místní prostředí integration runtime se připojit k oba zdroje a jímky úspěšně. Povolte pravidla pro každé úložiště dat, která je zahrnuta v operaci kopírování.

Například pro kopírování z **místní data ukládat do jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky**, proveďte následující kroky:

- Povolit odchozí **TCP** navázat komunikaci s portem **1433** pro bránu Windows firewall a podnikovou bránu firewall.
- Konfigurace nastavení brány firewall pro server Azure SQL na IP adresu místního prostředí integration runtime počítače přidat do seznamu povolených IP adres.

> [!NOTE]
> Pokud vaše brána firewall neumožňuje odchozí port 1433, místní prostředí integration runtime nemá přístup k Azure SQL přímo. V takovém případě můžete použít [připravené kopírování](copy-activity-performance.md) k databázi SQL Azure a datový Sklad SQL Azure. V tomto scénáři by v případě přesunu dat vyžadují jenom protokolu HTTPS (port 443).


## <a name="proxy-server-considerations"></a>Důležité informace o proxy serveru
Pokud vaše podnikové síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte příslušná nastavení proxy serveru používat místní prostředí integration runtime. Během fáze počáteční registraci můžete nastavit proxy server.

![Zadejte proxy server](media\create-self-hosted-integration-runtime\specify-proxy.png)

Místní prostředí integration runtime používá proxy server pro připojení ke cloudové službě. Klikněte na odkaz změnit při počátečním nastavení. Zobrazí se dialogové okno nastavení proxy serveru.

![Nastavení proxy serveru](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Existují tři možnosti konfigurace:

- **Nepoužívat proxy server**: místní prostředí integration runtime připojit ke cloudovým službám explicitně nepoužívá žádné proxy.
- **Použít systémový proxy server**: proxy nastavení, které je nakonfigurované v diahost.exe.config a diawp.exe.config používá modul runtime integrace v místním prostředí. Pokud žádný proxy server je nakonfigurován diahost.exe.config a diawp.exe.config, místní prostředí integration runtime připojí ke cloudové službě přímo bez proxy serveru.
- **Použít vlastní proxy server**: Konfigurace nastavení pro místní prostředí integration runtime, namísto použití konfigurace v diahost.exe.config a diawp.exe.config proxy HTTP. Vyžaduje se adresa a Port. Uživatelské jméno a heslo jsou volitelné na základě nastavení ověřování váš proxy server. Všechna nastavení jsou šifrované pomocí rozhraní Windows DPAPI na místní prostředí integration runtime a ukládají místně na počítači.

Hostitelská služba modulu integration runtime se restartuje automaticky po uložení aktualizované nastavení proxy serveru.

Po místní prostředí integration runtime byl úspěšně zaregistrován, pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru, použijte Správce konfigurace modulu Integration Runtime.

1. Spuštění **Správce konfigurace modulu Microsoft Integration Runtime**.
   - Přepněte na kartu **Nastavení**.
   - Klikněte na tlačítko **změnu** odkaz v **proxy server HTTP** části ke spuštění **nastavit proxy server HTTP** dialogového okna.
   - Po klepnutí **Další** tlačítko, se zobrazí dialogové okno upozornění s žádostí o vaše svolení k uložení nastavení proxy serveru a restartovat hostitelskou službu modulu Integration Runtime.

Můžete zobrazit a aktualizovat server proxy protokolu HTTP pomocí nástroje Configuration Manager.

![Zobrazení proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Pokud nastavení proxy serveru pomocí ověřování NTLM prostředí Integration runtime hostitelská služba spouští pod účtem domény. Pokud změníte heslo pro účet domény, později, nezapomeňte aktualizovat nastavení konfigurace pro službu a restartujte ji odpovídajícím způsobem. Z důvodu tohoto požadavku doporučujeme že použít vyhrazený doménový účet pro přístup k proxy serveru, který nevyžaduje často aktualizujte heslo.

### <a name="configure-proxy-server-settings"></a>Konfigurace nastavení proxy serveru

Pokud vyberete **použít systémový proxy server** nastavení pro proxy server HTTP, místní prostředí integration runtime používá nastavení proxy v diahost.exe.config a diawp.exe.config. Pokud v diahost.exe.config a diawp.exe.config není zadán žádný proxy server, místní prostředí integration runtime připojí ke cloudové službě přímo bez proxy serveru. Následující postup obsahuje pokyny pro aktualizaci diahost.exe.config souboru.

1. V Průzkumníku souborů vytvořte kopii bezpečné C:\Program Files\Microsoft integrace Runtime\3.0\Shared\diahost.exe.config zálohovat původní soubor.
2. Spusťte Notepad.exe spuštěný v režimu správce a otevřete textový soubor "C:\Program Files\Microsoft integrace Runtime\3.0\Shared\diahost.exe.config. Najít výchozí značka pro system.net, jak je znázorněno v následujícím kódu:

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

    Další vlastnosti jsou uvnitř značky proxy povoleno zadat požadované nastavení, jako je scriptLocation. Zobrazit [proxy – Element (nastavení sítě)](https://msdn.microsoft.com/library/sa91de1e.aspx) syntaxi.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Konfigurační soubor uložte do původního umístění a potom restartujte službu místním hostitelský modul Runtime integrace, který převezme tyto změny. Restartujte službu: pomocí apletu služby v Ovládacích panelech nebo z **Správce konfigurace modulu Integration Runtime** > klikněte na tlačítko **zastavit službu** tlačítko a pak klikněte na tlačítko **Start Služba**. Pokud služba nespustí, je pravděpodobné, že nesprávná syntaxe značky XML byl přidán do konfiguračního souboru aplikace, které proběhly.

> [!IMPORTANT]
> Nezapomeňte aktualizovat diahost.exe.config a diawp.exe.config.

Kromě těchto bodů musíte také ověřte, že Microsoft Azure je v seznamu povolených IP adres vaší společnosti. Seznam platných adres IP adres Microsoft Azure si můžete stáhnout z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Je to možné příznaky u problémů souvisejících se server brány firewall a proxy
Pokud narazíte na chyby podobné následující dotazy, je pravděpodobně v důsledku nesprávné konfigurace brány firewall nebo proxy serveru, který blokuje místní prostředí integration runtime v připojení ke službě Data Factory ke svému ověření. Přečtěte si předchozí část, aby vaše brány firewall a proxy serveru jsou správně nakonfigurované.

1. Při pokusu o registraci místního prostředí integration runtime se zobrazí následující chybová zpráva: "se nepodařilo registrovat tento uzel Integration Runtime. Zkontrolujte, jestli je platný ověřovací klíč a hostitelskou službu modulu Integration Service běží na tomto počítači. "
   - Když otevřete Správce konfigurace modulu Integration Runtime se zobrazí stav "**odpojeno**"nebo"**připojení**". Při prohlížení protokolů událostí Windows, v části "Prohlížeč událostí" > "Aplikace a služby protokoly" > "Microsoft Integration Runtime", se zobrazí chybové zprávy, jako je například chybová zpráva:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Povolit vzdálený přístup ze intranetu  
V případě, pokud používáte **PowerShell** nebo **aplikace Správce přihlašovacích údajů** pro šifrování přihlašovacích údajů z jiného počítače (v síti), než kde místní prostředí integration runtime je nainstalován, pak by vyžadovaly **vzdálený přístup z intranetu** polovení možnosti. Pokud spustíte **Powershellu** nebo **aplikace Správce přihlašovacích údajů** pro šifrování přihlašovacích údajů ve stejném počítači, kde místní prostředí integration runtime je nainstalován, pak **"vzdálený přístup z intranetu "** asi není zapnuté.

Vzdálený přístup z intranetu by měl být **povolené** před přidáním jiný uzel pro **vysokou dostupnost a škálovatelnost**.  

Instalaci místní prostředí integration runtime (v 3.3.xxxx.x a vyšší), ve výchozím nastavení, se zakáže instalace prostředí integration runtime **vzdálený přístup z intranetu** na počítači s modulem runtime integrace v místním prostředí.

Pokud používáte bránu firewall jiného dodavatele, můžete ručně otevřete port 8060 (nebo uživatel nakonfigurovaný port). Pokud narazíte na problém brány firewall během instalace modulu runtime integrace v místním prostředí, můžete pomocí následujícího příkazu k instalaci místní prostředí integration runtime bez konfigurace brány firewall.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **Aplikace Správce přihlašovacích údajů** ještě není k dispozici pro šifrování přihlašovacích údajů v ADFv2. Tato podpora přidáme později.  

Pokud zvolíte ne pro otevření portu 8060 na počítači s modulem runtime integrace v místním prostředí, použijte mechanismy pro zaslání než pomocí ** nastavení přihlašovacích údajů ** nakonfigurovat přihlašovací údaje úložiště dat aplikace. Například můžete použít rutinu New-AzureRmDataFactoryV2LinkedServiceEncryptCredential Powershellu. Najdete v části Nastavení přihlašovacích údajů a zabezpečení na tom, jak data uložených přihlašovacích údajů může být nastavena.


## <a name="next-steps"></a>Další postup
Projděte si následující kurz pro podrobné pokyny: [kurz: kopírování místních dat do cloudu](tutorial-hybrid-copy-powershell.md).
