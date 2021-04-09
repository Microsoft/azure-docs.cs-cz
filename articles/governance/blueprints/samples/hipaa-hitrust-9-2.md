---
title: Ukázka přehledu HIPAA HITRUST 9,2 podrobného plánu
description: Přehled ukázky podrobného plánu HIPAA HITRUST 9,2 Tento ukázkový plán pomůže zákazníkům vyhodnotit konkrétní ovládací prvky HIPAA HITRUST 9,2.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: bd65b6113f291457096bacc02bdbcfd92d6e0f84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98915539"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>Ukázka HIPAA HITRUST 9,2 podrobný plán

Ukázka HIPAA HITRUST 9,2 podrobného plánu poskytuje mechanismy správného řízení pomocí [Azure Policy](../../policy/overview.md) , které vám pomůžou vyhodnotit konkrétní ovládací prvky HIPAA HiTRUST 9,2. Tento podrobný plán pomůže zákazníkům nasadit základní sadu zásad pro všechny architektury nasazené v Azure, které musí implementovat ovládací prvky HIPAA HITRUST 9,2.

## <a name="control-mapping"></a>Mapování kontrol

[Mapování ovládacího prvku Azure Policy](../../policy/samples/hipaa-hitrust-9-2.md) poskytuje podrobné informace o definicích zásad zahrnutých v tomto podrobném plánu a o tom, jak se tyto definice zásad mapují k **doménám** a **ovládacím prvkům** kompatibility v HIPAA HiTRUST 9,2. Při přiřazení k architektuře jsou prostředky vyhodnocovány Azure Policym při nedodržení předpisů s přiřazenými definicemi zásad. Další informace najdete v tématu [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Nasadit

K nasazení ukázky Azure modrotisky HIPAA HITRUST 9,2 podrobného plánu je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte ukázku **HIPAA HiTRUST** Details a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázky HIPAA HiTRUST 9,2 details.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Projděte si seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

### <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s ovládacími prvky HIPAA HITRUST 9,2.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Zadejte **poznámky ke změnám** , jako je například "první verze publikovaná z ukázky podrobného plánu HIPAA HiTRUST 9,2." Na konci stránky pak vyberte **Publikovat**.

### <a name="assign-the-sample-copy"></a>Přiřazení ukázkové kopie

Po úspěšném **publikování** kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku zadáte parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán**.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každé z nich pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Podle potřeby ho změňte nebo ponechte.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice** podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Zamknout přiřazení

     Vyberte nastavení uzamčení podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte **Přiřadit** v dolní části stránky. Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobného plánu jsou **bezplatné**. U prostředků Azure se [cena stanovuje podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

### <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktů podrobného plánu:

|Název artefaktu |Název parametru |Description |
|---|---|---|
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Přístup přes internetový koncový bod by měl být omezený. |Povolit nebo zakázat pravidla pro monitorování příchozích NSG pravidel |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Účty: Stav účtu hosta |Určuje, jestli je účet místního hosta zakázaný. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací. |Umožňuje povolit nebo zakázat monitorování povolených aplikací v Azure Security Center |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Povolení současných připojení k Internetu nebo k doméně systému Windows |Určete, jestli se má počítačům zabránit v připojení k síti založené na doméně a síti, která není založená na doméně. Hodnota 0 umožňuje souběžná připojení a hodnotu 1 blokuje. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Aplikace API by měla být přístupná jenom přes protokol HTTPS v2 |Umožňuje povolit nebo zakázat monitorování používání protokolu HTTPS v API App v2. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Názvy aplikací (podporují zástupné znaky) |Středníkem oddělený seznam názvů aplikací, které by měly být nainstalovány. například ' Microsoft SQL Server 2014 (64-bit); Microsoft Visual Studio kód ' nebo ' Microsoft SQL Server 2014 * ' (pro vyhledání aplikace začínající na ' Microsoft SQL Server 2014 ') |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Auditovat ukončení procesu |Určuje, zda jsou při ukončení procesu generovány události auditu. Doporučuje se pro monitorování ukončení kritických procesů. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Auditování neomezeného síťového přístupu k účtům úložiště |Umožňuje povolit nebo zakázat monitorování síťového přístupu k účtu úložiště. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Audit: Není-li možno protokolovat audity zabezpečení, vypnout okamžitě systém |Audituje, zda dojde k vypnutí systému, když nebude možné protokolovat události zabezpečení. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Kryptografické otisky certifikátů |Středníkem oddělený seznam kryptografických otisků certifikátů, které by měly existovat v rámci důvěryhodného kořenového úložiště certifikátů (CERT: \ LocalMachine\Root). např. THUMBPRINT1; THUMBPRINT2; THUMBPRINT3 |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |V účtech Batch by měly být povolené diagnostické protokoly. |Povolení nebo zakázání monitorování diagnostických protokolů v účtech Batch |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Měly by být povolené diagnostické protokoly v centru událostí. |Umožňuje povolit nebo zakázat monitorování diagnostických protokolů v účtech centra událostí. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Měly by být povolené diagnostické protokoly ve vyhledávacích službách. |Povolí nebo zakáže sledování diagnostických protokolů ve službě Azure Search Service. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets. |Povolí nebo zakáže monitorování diagnostických protokolů v Service Fabric |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Na virtuálních počítačích by se mělo použít šifrování disku |Povolení nebo zakázání monitorování pro šifrování disků virtuálního počítače |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Povolit nezabezpečená přihlášení hosta |Určuje, jestli bude klient SMB umožňovat nezabezpečená přihlášení hostů k serveru SMB. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby |Povolení nebo zakázání monitorování sítě s přístupem jen v čase |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Porty pro správu by měly být uzavřeny na virtuálních počítačích |Povolí nebo zakáže monitorování otevřených portů pro správu na Virtual Machines |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA. |Umožňuje povolit nebo zakázat monitorování MFA pro účty s oprávněním k zápisu v předplatném. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování. |Umožňuje povolit nebo zakázat monitorování MFA pro účty s oprávněním vlastníka v předplatném. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Přístup k síti: Vzdáleně přístupné cesty registru |Určuje, které cesty k registru budou přístupné přes síť bez ohledu na uživatele nebo skupiny uvedené v seznamu řízení přístupu (ACL) `winreg` klíče registru. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Přístup k síti: vzdáleně přístupné cesty registru a dílčí cesty |Určuje, k jakým cestám a podcestám registru budou mít přístup přes síť bez ohledu na uživatele nebo skupiny uvedené v seznamu řízení přístupu (ACL) `winreg` klíče registru. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Přístup k síti: Sdílené složky, ke kterým lze přistupovat anonymně |Určuje, ke kterým síťovým sdíleným složkám mají přístup anonymní uživatelé. Výchozí konfigurace pro toto nastavení zásad má malý vliv, protože všichni uživatelé musí být ověřeni předtím, než budou moct přistupovat ke sdíleným prostředkům na serveru. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Konzola pro zotavení: povolení kopírování disket a přístup ke všem jednotkám a všem složkám |Určuje, zda má být k dispozici příkaz pro nastavení konzoly pro obnovení, který umožňuje nastavení proměnných prostředí konzoly pro obnovení. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Pro aplikaci API by mělo být vypnuto vzdálené ladění. |Umožňuje povolit nebo zakázat monitorování vzdáleného ladění pro aplikaci API. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Vzdálené ladění by mělo být pro webovou aplikaci vypnuté. |Umožňuje povolit nebo zakázat monitorování vzdáleného ladění pro webovou aplikaci. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Požadovaná doba uchování (ve dnech) pro protokoly v účtech Batch |Požadovaná doba uchování v diagnostických protokolech v dnech |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Požadovaná doba uchování (ve dnech) protokolů ve službě Azure Search Service |Požadovaná doba uchování v diagnostických protokolech v dnech |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Požadovaná doba uchování (ve dnech) protokolů v účtech centra událostí |Požadovaná doba uchování v diagnostických protokolech v dnech |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Název skupiny prostředků pro účet úložiště (musí existovat) pro nasazení nastavení diagnostiky pro skupiny zabezpečení sítě |Skupina prostředků, ve které se bude účet úložiště vytvořit. Tato skupina prostředků už musí existovat. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |V Kubernetes službách by se měla používat Access Controla RBAC (Role-Based) |Povolí nebo zakáže monitorování Kubernetes Services bez povolených RBAC. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče. |Povolte nebo zakažte sledování transparentní šifrování dat (TDE) s vlastní podporou klíčů. TDE s vlastní klíčovou podporou přináší větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Ochrana TDE systému SQL Server by měla být zašifrovaná pomocí vlastního klíče |Povolte nebo zakažte sledování transparentní šifrování dat (TDE) s vlastní podporou klíčů. TDE s vlastní klíčovou podporou přináší větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Předpona účtu úložiště pro účet místního úložiště pro nasazení nastavení diagnostiky pro skupiny zabezpečení sítě |Tato předpona bude kombinována s umístěním skupiny zabezpečení sítě, aby vytvořila název vytvořeného účtu úložiště. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets. |Povolení nebo zakázání škálování sady virtuálních počítačů, které hlásí aktualizace systému |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets. |Povolení nebo zakázání škálování sady virtuálních počítačů, které hlásí aktualizace systému |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Vypnutí překladu názvů vícesměrového vysílání |Určuje, jestli je povolený protokol LLMNR, sekundární protokol překladu IP adres, který se přenáší pomocí vícesměrového vysílání přes propojení místní podsítě v jedné podsíti. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager. |Umožňuje povolit nebo zakázat monitorování virtuálních počítačů s klasickým výpočetním prostředím. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení. |Povolení nebo zakázání monitorování slabých míst v operačním systému virtuálních počítačů Scale Sets |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení. |Povolit nebo zakázat detekci ohrožení zabezpečení virtuálních počítačů pomocí řešení posouzení ohrožení zabezpečení |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení |Auditovat spravované instance SQL, u kterých nejsou povolené kontroly opakovaného posuzování ohrožení zabezpečení Posouzení ohrožení zabezpečení může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (doména): použít místní pravidla brány firewall |Určuje, jestli mají místní správci povoleno vytvářet místní pravidla brány firewall, která se vztahují na pravidla brány firewall nakonfigurovaná Zásady skupiny pro profil domény. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (doména): chování pro odchozí připojení |Určuje chování pro odchozí připojení pro profil domény, které neodpovídají odchozímu pravidlu brány firewall. Výchozí hodnota 0 znamená, že se připojení povolí, a hodnota 1 znamená, že se připojení zablokují. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (doména): chování pro odchozí připojení |Určuje chování pro odchozí připojení pro profil domény, které neodpovídají odchozímu pravidlu brány firewall. Výchozí hodnota 0 znamená, že se připojení povolí, a hodnota 1 znamená, že se připojení zablokují. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (doména): zobrazení oznámení |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením zobrazuje oznámení pro uživatele, když se zablokuje příjem příchozích připojení pro profil domény. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (doména): použít nastavení profilu |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením používá nastavení pro profil domény pro filtrování síťového provozu. Pokud vyberete vypnout, brána Windows Firewall s pokročilým zabezpečením nebude pro tento profil používat žádná pravidla firewallu ani pravidla zabezpečení připojení. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (privátní): použití pravidel zabezpečení místních připojení |Určuje, jestli můžou místní správci vytvářet pravidla zabezpečení připojení, která se vztahují na pravidla zabezpečení připojení nakonfigurovaná Zásady skupiny pro privátní profil. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (privátní): použít pravidla místní brány firewall |Určuje, jestli mají místní správci povoleno vytvářet místní pravidla brány firewall, která se vztahují na pravidla brány firewall nakonfigurovaná Zásady skupiny pro privátní profil. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (privátní): chování pro odchozí připojení |Určuje chování pro odchozí připojení pro privátní profil, které neodpovídají odchozímu pravidlu brány firewall. Výchozí hodnota 0 znamená, že se připojení povolí, a hodnota 1 znamená, že se připojení zablokují. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (privátní): zobrazení oznámení |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením zobrazuje oznámení uživateli, když se zablokuje příjem příchozích připojení pro privátní profil. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (privátní): použít nastavení profilu |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením používá nastavení privátního profilu pro filtrování síťového provozu. Pokud vyberete vypnout, brána Windows Firewall s pokročilým zabezpečením nebude pro tento profil používat žádná pravidla firewallu ani pravidla zabezpečení připojení. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (veřejná): použití pravidel zabezpečení místních připojení |Určuje, jestli můžou místní správci vytvářet pravidla zabezpečení připojení, která se vztahují na pravidla zabezpečení připojení nakonfigurovaná Zásady skupiny pro veřejný profil. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (veřejná): použít pravidla místní brány firewall |Určuje, jestli mají místní správci povoleno vytvářet místní pravidla brány firewall, která se vztahují na pravidla brány firewall nakonfigurovaná Zásady skupiny pro veřejný profil. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (veřejná): chování pro odchozí připojení |Určuje chování pro odchozí připojení pro veřejný profil, které neodpovídají odchozímu pravidlu brány firewall. Výchozí hodnota 0 znamená, že se připojení povolí, a hodnota 1 znamená, že se připojení zablokují. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (veřejná): zobrazení oznámení |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením zobrazuje oznámení uživateli, když se zablokuje příjem příchozích připojení u programu pro veřejný profil. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall (veřejná): použít nastavení profilu |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením používá nastavení pro veřejný profil k filtrování síťového provozu. Pokud vyberete vypnout, brána Windows Firewall s pokročilým zabezpečením nebude pro tento profil používat žádná pravidla firewallu ani pravidla zabezpečení připojení. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall: doména: povoluje jednosměrovou odpověď. |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením umožňuje místnímu počítači přijímat odpovědi jednosměrového vysílání do odchozích zpráv vícesměrového vysílání nebo všesměrového vysílání. pro profil domény. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall: privátní: povoluje jednosměrovou odpověď |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením umožňuje místnímu počítači přijímat odpovědi jednosměrového vysílání do odchozích zpráv vícesměrového vysílání nebo všesměrového vysílání. pro privátní profil. |
|Auditovat HITRUST/HIPAA ovládací prvky a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit |Brána Windows Firewall: veřejná: povolení odezvy jednosměrového vysílání |Určuje, jestli brána Windows Firewall s pokročilým zabezpečením umožňuje místnímu počítači přijímat odpovědi jednosměrového vysílání do odchozích zpráv vícesměrového vysílání nebo všesměrového vysílání. pro veřejný profil. |

## <a name="next-steps"></a>Další kroky

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
