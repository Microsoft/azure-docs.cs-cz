---
title: Ukázka podrobného plánu CMMC úrovně 3
description: Přehled ukázky podrobného plánu CMMC úrovně 3. Tento ukázkový podrobný plán pomáhá zákazníkům vyhodnotit konkrétní kontroly.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572603"
---
# <a name="cmmc-level-3-blueprint-sample"></a>Ukázka podrobného plánu CMMC úrovně 3

Ukázka podrobného plánu CMMC úrovně 3 poskytuje ochranné mechanismy správného řízení pomocí [Azure Policy](../../policy/overview.md) , které vám pomůžou vyhodnotit konkrétní ovládací prvky [architektury CMMC (kyberbezpečnosti model)](https://www.acq.osd.mil/cmmc/index.html) . Tento podrobný plán pomůže zákazníkům nasadit základní sadu zásad pro všechny architektury nasazené v Azure, které musí implementovat ovládací prvky pro CMMC úrovně 3.

## <a name="control-mapping"></a>Mapování kontrol

[Mapování ovládacího prvku Azure Policy](../../policy/samples/cmmc-l3.md) poskytuje podrobné informace o definicích zásad zahrnutých v tomto podrobném plánu a způsobu mapování těchto definic zásad na **ovládací prvky** v rozhraní CMMC Framework. Při přiřazení k architektuře jsou prostředky vyhodnocovány Azure Policym při nedodržení předpisů s přiřazenými definicemi zásad. Další informace najdete v tématu [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Nasadit

Pokud chcete nasadit ukázku plánu Azure modrotisky CMMC úrovně 3, je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte ukázku podrobného plánu **CMMC úrovně 3** a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázky podrobného plánu CMMC úrovně 3.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Zkontrolujte seznam artefaktů, které jsou zahrnuty v ukázce podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

### <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s ovládacími prvky CMMC úrovně 3.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Zadejte **poznámky ke změnám** , jako je například první verze publikovaná z ukázky podrobného plánu CMMC úrovně 3. Na konci stránky pak vyberte **Publikovat**.

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

|Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|CMMC úrovně 3|Přiřazení zásad|Při vyhodnocování zásad konfigurace hosta zahrnout servery připojené k Arc|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k Arc účtovat každý měsíc; Další informace najdete na https://aka.ms/policy-pricing|
|CMMC úrovně 3|Přiřazení zásad|Seznam uživatelů, kteří musí být vyloučení ze skupiny správců virtuálních počítačů s Windows|Středníkem oddělený seznam uživatelů, kteří by měli být vyloučení v místní skupině Administrators; Např.: Správce; myUser1; myUser2|
|CMMC úrovně 3|Přiřazení zásad|Seznam uživatelů, které musí být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam uživatelů, kteří by měli být zahrnutí do místní skupiny Administrators; Např.: Správce; myUser1; myUser2|
|CMMC úrovně 3|Přiřazení zásad|ID pracovního prostoru Log Analytics pro vytváření sestav agenta virtuálního počítače|ID (GUID) Log Analytics pracovního prostoru, kde by se měly agenti virtuálních počítačů hlásit|
|CMMC úrovně 3|Přiřazení zásad|Povolené názvy eliptické křivky|Seznam povolených názvů křivek pro certifikáty kryptografie s eliptickou křivkou|
|CMMC úrovně 3|Přiřazení zásad|Povolené typy klíčů|Seznam povolených typů klíčů|
|CMMC úrovně 3|Přiřazení zásad|Povolení využití sítě hostitele pro Kubernetes clustery|Tuto hodnotu nastavte na true, pokud je v případě, že je povoleno používat síť hostitele v opačném případě false.|
|CMMC úrovně 3|Přiřazení zásad|Auditovat změnu zásad ověřování|Určuje, jestli se při provádění změn v zásadách ověřování generují události auditu. Toto nastavení je užitečné ke sledování změn ve vztahu důvěryhodnosti na úrovni domény a doménové struktury, které jsou udělené uživatelským účtům nebo skupinám.|
|CMMC úrovně 3|Přiřazení zásad|Auditovat změnu zásad autorizace|Určuje, jestli se mají generovat události auditu pro přiřazení a odebrání uživatelských práv v zásadách uživatelských práv, změnách oprávnění objektu tokenu zabezpečení, změnách atributů prostředku a změnách zásad centrálního přístupu pro objekty systému souborů.|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Azure Backup by měl být povolený pro Virtual Machines|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: účty Cognitive Services by měly omezovat přístup k síti|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: spravované instance SQL by měly používat klíče spravované zákazníkem k šifrování neaktivních dat|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Azure API for FHIR by měl používat klíč spravovaný zákazníkem (CMK) k šifrování neaktivních dat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Brána Firewall webových aplikací (WAF) by měla být povolená pro službu Azure front-dveří|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: nastavení posouzení ohrožení zabezpečení pro SQL Server by měla obsahovat e-mailovou adresu pro příjem sestav kontroly.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásady: přístup k veřejné síti by měl být pro účty Cognitive Services zakázaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: CORS by neměl umožňovat každému prostředku přístup k aplikacím funkcí|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: doporučení adaptivního posílení zabezpečení sítě by se měla použít u virtuálních počítačů s přístupem k Internetu.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: k vašemu předplatnému by měl být přiřazený víc než jeden vlastník.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: na virtuálních počítačích by se mělo použít šifrování disku|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měla by být povolená e-mailová oznámení vlastníkovi předplatného s vysokou závažností.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Trezor klíčů by měl mít povolenou ochranu vyprázdnění.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: servery SQL by měly používat klíče spravované zákazníkem k šifrování neaktivních dat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být pro aplikace Function App vypnuté.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro Key Vault.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: měla by být povolená geograficky redundantní záloha pro Azure Database for MariaDB|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: CORS by neměl všem doménám umožňovat přístup k rozhraní API pro FHIR.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: počítače s Windows by měly splňovat požadavky na možnosti zabezpečení zabezpečení sítě.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: pravidla povolených v zásadách adaptivního řízení aplikací by se měla aktualizovat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Firewall webových aplikací (WAF) by měl používat zadaný režim pro Application Gateway|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: klíče by měly mít nastavená data vypršení platnosti.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měla by být povolená transparentní šifrování dat na databázích SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Profil Azure Monitorho protokolu by měl shromažďovat protokoly pro kategorie Write, DELETE a Action.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: posouzení ohrožení zabezpečení by mělo být povolené na spravované instanci SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' verze PHP ' je nejnovější, pokud se používá jako součást aplikace API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Trezor klíčů by měl mít povolené obnovitelné odstranění.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: správce Azure Active Directory by měl zřídit pro SQL servery.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měla by být povolená jenom zabezpečená připojení k mezipaměti Azure cache pro Redis.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro Azure Database for PostgreSQL servery by mělo být povolené šifrování infrastruktury.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: řešení Endpoint Protection by se mělo nainstalovat na Virtual Machine Scale Sets|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro App Service.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: počítače s Windows by měly splňovat požadavky na zásady auditu systému – změna zásad|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: účty Cognitive Services by měly povolit šifrování dat|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: přístup přes SSH z Internetu by měl být blokovaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: nepřipojené disky by měly být zašifrované|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měla by být povolený Azure Defender pro úložiště.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: účty úložiště by měly omezit síťový přístup|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: CORS by neměl umožňovat každému prostředku přístup k vaší aplikaci API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: nasazení rozšířené ochrany před internetovými útoky na účty úložiště|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: proměnné účtu služby Automation by měly být zašifrované|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v IoT Hub.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro Azure Database for MySQL servery by mělo být povolené šifrování infrastruktury.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace zabezpečení by měla existovat výstraha protokolu aktivit (Microsoft. Security/securitySolutions/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: Oprava chyb v konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by měla být opravena|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: počítače s Windows by měly splňovat požadavky na možnosti zabezpečení – síťový přístup|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: mělo by se povolit zabezpečený přenos do účtů úložiště.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: Azure Monitor by měl shromažďovat protokoly aktivit ze všech oblastí|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Firewall webových aplikací (WAF) by měl používat zadaný režim pro službu Azure front-dveří.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: účty úložiště by měly mít šifrování infrastruktury|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: v počítačích by měla být povolená Adaptivní řízení aplikací pro definování bezpečných aplikací.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: měla by být povolená geograficky redundantní záloha pro Azure Database for PostgreSQL|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: počítače s Windows by měly splňovat požadavky na možnosti zabezpečení – řízení uživatelských účtů.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást webové aplikace.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro servery.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro vaše předplatné by se měla určit maximálně 3 vlastníci.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: odběry by měly mít kontaktní e-mailovou adresu pro problémy se zabezpečením.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: veřejný přístup k účtu úložiště by neměl být povolený.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro Kubernetes.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Brána firewall by měla být povolená na Key Vault|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Firewall webových aplikací (WAF) by měl být povolený pro Application Gateway|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: Auditovat počítače s Windows, které umožňují opakované použití předchozích 24 hesel|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Registry kontejneru by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK).|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním k zápisu by se měly z vašeho předplatného odebrat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: přístup k veřejné síti by měl být pro PostgreSQL flexibilní servery zakázaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: ohrožení zabezpečení v Azure Container Registrych imagí by mělo být opraveno.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním ke čtení by se měly z vašeho předplatného odebrat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro SQL servery na počítačích.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: účty Cognitive Services by měly povolit šifrování dat pomocí klíče spravovaného zákazníkem|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: zastaralé účty by měly být odebrány z vašeho předplatného|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Function App by měl být dostupný jenom přes HTTPS|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měla by být povolená e-mailová oznámení pro výstrahy s vysokou závažností.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: účet úložiště by měl používat klíč spravovaný zákazníkem pro šifrování.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást webové aplikace.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást aplikace Function App.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' verze PHP ' je nejnovější, pokud se používá jako součást webové aplikace.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást aplikace API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: klíče by měly být zadaného kryptografického typu RSA nebo ES|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: předplatná Azure by měla mít profil protokolu aktivit.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: v clusterech služby Azure Kubernetes by měly být zašifrované jak operační systémy, tak datové disky, které se spravují pomocí zákaznických klíčů.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měly by být povolené Azure Defender pro Azure SQL Database servery.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: šifrování Azure Průzkumník dat v klidovém umístění by mělo používat klíč spravovaný zákazníkem|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: klíče využívající šifrování RSA by měly mít zadanou minimální velikost klíče.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: měla by být povolená geograficky redundantní záloha pro Azure Database for MySQL|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: Kubernetes clustery by měly používat jenom schválenou síť hostitele a rozsah portů.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: aktualizace systému by se měly nainstalovat na vaše počítače.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: počítače s Windows by měly splňovat požadavky na zásady auditu systému – použití oprávnění|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: úlohy Azure Stream Analytics by měly používat klíče spravované zákazníkem k šifrování dat|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást aplikace API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP Version ' je nejnovější, pokud se používá ke spuštění webové aplikace|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: v aplikaci API by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: u vašeho předplatného by měla být povolená možnost MFA s oprávněními k zápisu.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP verze ' je nejnovější, pokud se používá ke spuštění aplikace API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: rozšíření Microsoft IaaSAntimalware Extension by mělo být nasazeno na serverech Windows|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást aplikace Function App.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: všechny síťové porty by měly být omezené na skupinách zabezpečení sítě přidružených k vašemu virtuálnímu počítači.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měla by být vybraná cenová úroveň Security Center Standard.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: auditujte počítače s Windows, které neomezují minimální délku hesla na 14 znaků.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: použití auditu pro vlastní pravidla RBAC|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: webová aplikace by měla být přístupná jen přes protokol HTTPS|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: mělo by se povolit auditování na SQL serveru.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: vícefaktorové ověřování by mělo být povolené u účtů s oprávněním vlastníka u vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: rozšířené zabezpečení dat by mělo být na vašich serverech SQL povolené.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: rozšířené zabezpečení dat by mělo být povolené na spravované instanci SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: Role-Based Access Control (RBAC) by se mělo používat pro služby Kubernetes Services.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: virtuální počítače by měly mít příponu konfigurace hosta|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: monitorovat chybějící Endpoint Protection v Azure Security Center|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: protokol aktivit by se měl uchovávat aspoň po dobu jednoho roku.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: přístup k veřejné síti by měl být pro PostgreSQL servery zakázaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: nasazení rozšířené ochrany před internetovými útoky pro účty Cosmos DB|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v App Services.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: aplikace API by měla být přístupná jen přes protokol HTTPS|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy by měla existovat výstraha protokolu aktivit (Microsoft. ClassicNetwork/networkSecurityGroups/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy by měla existovat výstraha protokolu aktivit (Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete)|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy (Microsoft. Network/networkSecurityGroups/Delete) by měla existovat výstraha protokolu aktivit.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy by měla existovat výstraha protokolu aktivit (Microsoft. Network/networkSecurityGroups/securityRules/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy by měla existovat výstraha protokolu aktivit (Microsoft. SQL/servery/firewallRules/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: virtuální počítače, které nejsou přístupné z Internetu, by měly být chráněné pomocí skupin zabezpečení sítě|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: audit počítačů s Windows, u kterých není povolené nastavení složitosti hesla|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: měla by být povolený Azure Defender pro Registry kontejnerů.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: úlohy Azure Data Box by měly povolit dvojité šifrování pro neaktivní neaktivní data na zařízení.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: je potřeba nainstalovat aktualizace systému na služby Virtual Machine Scale Sets.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: Microsoft Antimalware pro Azure by měl být nakonfigurovaný tak, aby automaticky aktualizoval podpisy ochrany.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace zásad by měla existovat výstraha protokolu aktivit (Microsoft. Authorization/policyAssignments/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: přístup k veřejné síti by měl být zakázán pro servery MySQL Flexible|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: účty úložiště by měly umožňovat přístup z důvěryhodných služeb Microsoftu|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být pro webové aplikace vypnuté.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: certifikáty používající kryptografii RSA by měly mít zadanou minimální velikost klíče.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Registry kontejneru by neměly umožňovat neomezený přístup k síti.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro databázové servery PostgreSQL by mělo být povoleno připojení SSL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: rozšíření konfigurace hosta by se mělo nasadit na virtuální počítače Azure se spravovanou identitou přiřazenou systémem.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: dlouhodobě geograficky redundantní zálohování by mělo být povolené pro databáze SQL Azure|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: přístup k veřejné síti by měl být pro servery MySQL zakázaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: Auditovat počítače s Windows, které neukládají hesla pomocí reverzibilního šifrování|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: počítače s Windows by měly splňovat požadavky na přiřazení uživatelských práv|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by měla být opravena|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP Version ' je nejnovější, pokud se používá ke spuštění aplikace Function App|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: vícefaktorové ověřování by mělo být povolené u účtů s oprávněním ke čtení u vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásadu: přístup k protokolu RDP z Internetu by měl být zablokovaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: auditujte počítače Linux, které nemají oprávnění k souboru passwd nastavené na 0644.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: podsítě by se měly přidružit ke skupině zabezpečení sítě.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: pro databázové servery MySQL by mělo být povoleno připojení SSL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by měla být opravena|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být vypnuté pro API Apps|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: auditujte počítače Linux, které umožňují vzdálená připojení z účtů bez hesel.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: zastaralé účty s oprávněním vlastníka by měly být odebrány z vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: na Azure Průzkumník dat by mělo být povolené šifrování typu Double.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: posouzení ohrožení zabezpečení by mělo být povoleno na vašich serverech SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: Agent Log Analytics by měl být nainstalovaný Virtual Machine Scale Sets|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: ve vaší webové aplikaci by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: šifrování disku by mělo být povolené v Azure Průzkumník dat|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: audit počítačů se systémem Linux, které mají účty bez hesla|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv zásad: pracovní prostory Azure synapse by měly používat klíče spravované zákazníky k šifrování neaktivních dat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: v Function App by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: služby Kubernetes by měly být upgradovány na nezranitelnou Kubernetes verzi|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: veškerý internetový provoz by měl být směrován prostřednictvím nasazené Azure Firewall|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: počítače se systémem Linux by měly splňovat požadavky na základní hodnoty zabezpečení Azure|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: přístup k veřejné síti by měl být pro MariaDB servery zakázaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Vliv na zásady: měla by být opravena ohrožení zabezpečení vašich databází SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Efekt pro zásady: klíče používající kryptografii eliptické křivky by měly mít zadané názvy křivek|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC úrovně 3|Přiřazení zásad|Obory názvů vyloučené z vyhodnocení zásad: Kubernetes clustery by měly používat jenom schválenou síť hostitele a rozsah portů.|Seznam oborů názvů Kubernetes pro vyloučení ze vyhodnocení zásad|
|CMMC úrovně 3|Přiřazení zásad|Nejnovější verze Java pro App Services|Nejnovější podporovaná verze Java pro App Services|
|CMMC úrovně 3|Přiřazení zásad|Nejnovější verze Pythonu pro Linux pro App Services|Nejnovější podporovaná verze Pythonu pro App Services|
|CMMC úrovně 3|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů, které se pro přidání do oboru při auditování Log Analytics nasazení agenta podporují s operačním systémem Linux.|Příklad hodnoty: '/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage '|
|CMMC úrovně 3|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru při auditování Log Analytics nasazení agenta|Příklad hodnoty: '/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage '|
|CMMC úrovně 3|Přiřazení zásad|Seznam oblastí, kde by měla být povolena Network Watcher|Audit, pokud pro oblasti nejsou Network Watcher povolené.|
|CMMC úrovně 3|Přiřazení zásad|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly||
|CMMC úrovně 3|Přiřazení zásad|Maximální hodnota v rozsahu povolených hostitelských portů, které může v rámci oboru názvů hostitele v síti používat lusky|Maximální hodnota v rozsahu povolených hostitelských portů, kterou může v oboru názvů hostitele v síti použít.|
|CMMC úrovně 3|Přiřazení zásad|Minimální velikost klíče RSA pro klíče|Minimální velikost klíče pro klíče RSA.|
|CMMC úrovně 3|Přiřazení zásad|Minimální velikost certifikátů klíče RSA|Minimální velikost klíče pro certifikáty RSA.|
|CMMC úrovně 3|Přiřazení zásad|Minimální verze protokolu TLS pro webové servery Windows|Webové servery Windows s nižšími verzemi TLS budou posouzené jako nevyhovující předpisům.|
|CMMC úrovně 3|Přiřazení zásad|Minimální hodnota v rozsahu povolených hostitelských portů, které může v oboru názvů hostitele v síti používat lusky|Minimální hodnota v rozsahu povolených hostitelských portů, které může v oboru názvů hostitele v síti používat lusky.|
|CMMC úrovně 3|Přiřazení zásad|Požadavek na režim|Režim vyžadovaný pro všechny zásady WAF|
|CMMC úrovně 3|Přiřazení zásad|Požadavek na režim|Režim vyžadovaný pro všechny zásady WAF|
|CMMC úrovně 3|Přiřazení zásad|Povolené cesty hostitele pro hostPath svazky v umístění, které se mají použít|Cesty hostitele povolené pro hostPath svazky pod. Zadejte prázdný seznam cest pro blokování všech cest hostitele.|
|CMMC úrovně 3|Přiřazení zásad|Přístup k síti: Vzdáleně přístupné cesty registru|Určuje, které cesty k registru budou přístupné přes síť bez ohledu na uživatele nebo skupiny uvedené v seznamu řízení přístupu (ACL) `winreg` klíče registru.|
|CMMC úrovně 3|Přiřazení zásad|Přístup k síti: vzdáleně přístupné cesty registru a dílčí cesty|Určuje, k jakým cestám a podcestám registru budou mít přístup přes síť bez ohledu na uživatele nebo skupiny uvedené v seznamu řízení přístupu (ACL) `winreg` klíče registru.|
|CMMC úrovně 3|Přiřazení zásad|Přístup k síti: Sdílené složky, ke kterým lze přistupovat anonymně|Určuje, ke kterým síťovým sdíleným složkám mají přístup anonymní uživatelé. Výchozí konfigurace pro toto nastavení zásad má malý vliv, protože všichni uživatelé musí být ověřeni předtím, než budou moct přistupovat ke sdíleným prostředkům na serveru.|
|CMMC úrovně 3|Přiřazení zásad|Zabezpečení sítě: Konfigurace typů šifrování povolených pro protokol Kerberos|Určuje typy šifrování, které smí protokol Kerberos používat.|
|CMMC úrovně 3|Přiřazení zásad|Zabezpečení sítě: Úroveň ověřování pro systém LAN Manager|Určete, který ověřovací protokol Challenge-Response se používá pro přihlášení k síti. Tato volba ovlivňuje úroveň protokolu ověřování používaného klienty, úroveň vyjednané zabezpečení relace a úroveň ověřování, kterou servery akceptují.|
|CMMC úrovně 3|Přiřazení zásad|Zabezpečení sítě: Požadavky na podepisování klienta LDAP|Zadejte úroveň podepisování dat, která je požadována jménem klientů, kteří vydávají požadavky LDAP BIND.|
|CMMC úrovně 3|Přiřazení zásad|Zabezpečení sítě: Minimální zabezpečení relace pro klienty založené na NTLM SSP (včetně zabezpečeného vzdáleného volání procedur)|Určuje chování, které klienti povolují pro aplikace využívající zprostředkovatele zabezpečení (SSP) NTLM. Rozhraní SSPI (SSP) se používá v aplikacích, které potřebují ověřovací služby. Další informace najdete v tématu [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers) .|
|CMMC úrovně 3|Přiřazení zásad|Zabezpečení sítě: Minimální zabezpečení relace pro servery založené na NTLM SSP (včetně zabezpečeného vzdáleného volání procedur)|Určuje chování, které servery povolují pro aplikace pomocí zprostředkovatele podpory zabezpečení (SSP) NTLM. Rozhraní SSPI (SSP) se používá v aplikacích, které potřebují ověřovací služby.|
|CMMC úrovně 3|Přiřazení zásad|Nejnovější verze PHP pro App Services|Nejnovější podporovaná verze PHP pro App Services|
|CMMC úrovně 3|Přiřazení zásad|Požadovaná doba uchování (dny) pro IoT Hub diagnostické protokoly||
|CMMC úrovně 3|Přiřazení zásad|Název skupiny prostředků pro Network Watcher|Název skupiny prostředků NetworkWatcher, například NetworkWatcherRG. Toto je skupina prostředků, ve které se nachází sledovací procesy sítě.|
|CMMC úrovně 3|Přiřazení zásad|Požadované nastavení auditování pro servery SQL||
|CMMC úrovně 3|Přiřazení zásad|Azure Data Box SKU, které podporují dvojité šifrování založené na softwaru|Seznam SKU Azure Data Box, které podporují šifrování založené na softwaru|
|CMMC úrovně 3|Přiřazení zásad|UAC: Režim schválení správcem pro předdefinovaný účet správce|Určuje chování režimu schválení správce pro předdefinovaný účet správce.|
|CMMC úrovně 3|Přiřazení zásad|Řízení uživatelských účtů: chování výzvy ke zvýšení oprávnění pro správce v režimu schválení správce|Určuje chování výzvy ke zvýšení oprávnění pro správce.|
|CMMC úrovně 3|Přiřazení zásad|UAC: zjištění instalace aplikace a výzva ke zvýšení oprávnění|Určuje chování při detekci instalace aplikace pro daný počítač.|
|CMMC úrovně 3|Přiřazení zásad|Řízení uživatelských účtů: spuštění všech správců v režimu schválení správce|Určuje chování všech nastavení zásad řízení uživatelských účtů (UAC) pro daný počítač.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé a skupiny, kteří můžou vynutit vypnutí ze vzdáleného systému|Určuje, kteří uživatelé a skupiny mohou vypnout počítač ze vzdáleného umístění v síti.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé a skupiny, kterým se zamítl přístup k tomuto počítači ze sítě|Určuje, kteří uživatelé nebo skupiny jsou výslovně zakázáni připojení k počítači v síti.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé a skupiny, kterým se zamítlo místní přihlášení|Určuje, kteří uživatelé a skupiny nejsou pro přihlášení k počítači výslovně povoleni.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé a skupiny, kterým se zamítlo přihlášení jako dávková úloha|Určuje, kteří uživatelé a skupiny nemají explicitně povolený přihlášení k počítači jako dávkovou úlohu (tj. naplánovaná úloha).|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé a skupiny, kterým se zakázalo přihlášení jako služba|Určuje, které účty služeb nejsou explicitně povoleny k registraci procesu jako služby.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé a skupiny, kterým se zamítlo přihlášení prostřednictvím služby Vzdálená plocha|Určuje, kteří uživatelé a skupiny nemají explicitně povolený přihlášení k počítači prostřednictvím Terminálové služby nebo klienta vzdálené plochy.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé a skupiny, které mohou obnovit soubory a adresáře|Určuje, kteří uživatelé a skupiny mají povolený přístup k souborům, adresářům, registrům a dalším oprávněním trvalých objektů při obnovování zálohovaných souborů a adresářů.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé a skupiny, kteří mohou vypnout systém|Určuje, kteří uživatelé a skupiny, kteří jsou místně přihlášení k počítačům ve vašem prostředí, mohou vypnout operační systém pomocí příkazu pro vypnutí.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé nebo skupiny, kteří se mohou místně přihlašovat|Určuje, kteří vzdálení uživatelé v síti mají povolené připojení k počítači. To nezahrnuje Připojení ke vzdálené ploše.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé nebo skupiny, které mohou zálohovat soubory a adresáře|Určuje, kteří uživatelé a skupiny můžou obejít oprávnění k souborům a složkám k zálohování systému.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé nebo skupiny, kteří mohou měnit systémový čas|Určuje, kteří uživatelé a skupiny mohou měnit čas a datum na vnitřních hodinách počítače.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé nebo skupiny, které mohou změnit časové pásmo|Určuje, kteří uživatelé a skupiny mohou měnit časové pásmo počítače.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé nebo skupiny, které mohou vytvořit objekt tokenu|Určuje, kteří uživatelé a skupiny mají povolený přístup k vytvoření přístupového tokenu, který může poskytnout zvýšená oprávnění pro přístup k citlivým datům.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé nebo skupiny, kteří se mohou místně přihlašovat|Určuje, kteří uživatelé nebo skupiny se mohou interaktivně přihlašovat k počítači. Uživatel, který se pokouší o přihlášení prostřednictvím Připojení ke vzdálené ploše nebo IIS, vyžaduje také toto uživatelské právo.|
|CMMC úrovně 3|Přiřazení zásad|Remote Desktop Users|Uživatelé nebo skupiny, kteří se mohou přihlašovat prostřednictvím služby Vzdálená plocha|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé nebo skupiny, které mohou spravovat auditování a protokol zabezpečení|Určuje, kteří uživatelé a skupiny mají povoleno měnit možnosti auditování pro soubory a adresáře a vymazat protokol zabezpečení.|
|CMMC úrovně 3|Přiřazení zásad|Uživatelé nebo skupiny, kteří mohou převzít vlastnictví souborů nebo jiných objektů|Určuje, kteří uživatelé a skupiny mohou převzít vlastnictví souborů, složek, klíčů registru, procesů nebo vláken. Toto uživatelské právo obchází všechna oprávnění, která jsou k ochraně objektů k dismístě pro zadaného uživatele.|

## <a name="next-steps"></a>Další kroky

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)