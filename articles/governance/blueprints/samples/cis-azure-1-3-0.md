---
title: Ukázka modelu Microsoft Azure CI 1.3.0 Foundation v testu výkonnosti
description: Přehled ukázky služby CI Microsoft Azure Foundation srovnávací v 1.3.0 details Tento ukázkový podrobný plán pomáhá zákazníkům vyhodnotit konkrétní kontroly.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: 7030b4e9e9ddd3d5bbf1a5a7deaf1e1aa241b38e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565992"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>Ukázka modelu Microsoft Azure CI 1.3.0 Foundation v testu výkonnosti

Ukázka 1.3.0 podrobného plánu pro SNS v Microsoft Azure modelu CI v systému poskytuje [Azure Policy](../../policy/overview.md) , která vám pomůžou vyhodnotit konkrétní doporučení modelu CI Microsoft Azure Foundation v 1.3.0. Tento podrobný plán pomůže zákazníkům nasadit základní sadu zásad pro jakoukoli architekturu nasazenou v Azure, která musí implementovat doporučení CIS v 1.3.0 s Microsoft Azure Foundation.

## <a name="recommendation-mapping"></a>Mapování doporučení

[Mapování doporučení Azure Policy](../../policy/samples/cis-azure-1-3-0.md) poskytuje podrobné informace o definicích zásad zahrnutých v tomto podrobném plánu a způsobu, jakým jsou tyto definice zásad mapovány k **doporučením** v CIS Microsoft Azure Foundation test v 1.3.0. Při přiřazení k architektuře jsou prostředky vyhodnocovány Azure Policym při nedodržení předpisů s přiřazenými definicemi zásad. Další informace najdete v tématu [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Nasadit

Pokud chcete nasadit ukázkovou 1.3.0 podrobného plánu v Azure Microsoft Azure modrotisky, je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte vzorový **test služby CIS Microsoft Azure Foundation 1.3.0** a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázky srovnávacího plánu pro CIS Microsoft Azure Foundation.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Zkontrolujte seznam artefaktů, které jsou zahrnuty v ukázce podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

### <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání pomocí CIS Microsoft Azure Foundation srovnávacího testu v 1.3.0.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Zadejte **poznámky ke změnám** , jako je například "první verze publikovaná z modelu CI Microsoft Azure Foundation srovnávací návrh testu." Na konci stránky pak vyberte **Publikovat**.

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
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Seznam rozšíření virtuálních počítačů, která jsou schválena pro použití|Středníkem oddělený seznam rozšíření virtuálních počítačů; Úplný seznam rozšíření zobrazíte pomocí příkazu Azure PowerShell Get-AzVMExtensionImage|
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: spravované instance SQL by měly používat klíče spravované zákazníkem k šifrování neaktivních dat|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: nastavení posouzení ohrožení zabezpečení pro SQL Server by měla obsahovat e-mailovou adresu pro příjem sestav kontroly.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v Azure Data Lake Store.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: na virtuálních počítačích by se mělo použít šifrování disku|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Trezor klíčů by měl mít povolenou ochranu vyprázdnění.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: Zajistěte, aby měla aplikace API klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: servery SQL by měly používat klíče spravované zákazníkem k šifrování neaktivních dat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: spravované identity by se měly používat v Function App|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro Key Vault.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: vlastní role vlastníků předplatného nesmí existovat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: klíče by měly mít nastavená data vypršení platnosti.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měla by být povolená transparentní šifrování dat na databázích SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: posouzení ohrožení zabezpečení by mělo být povolené na spravované instanci SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' verze PHP ' je nejnovější, pokud se používá jako součást aplikace API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: správce Azure Active Directory by měl zřídit pro SQL servery.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro App Service.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: účty úložiště by měly omezovat přístup k síti pomocí pravidel virtuální sítě|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: ve vaší webové aplikaci by se měla použít spravovaná identita.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: přístup přes SSH z Internetu by měl být blokovaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: nepřipojené disky by měly být zašifrované|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měla by být povolený Azure Defender pro úložiště.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: účty úložiště by měly omezit síťový přístup|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v Logic Apps.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v IoT Hub.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: FTPS by se měla vyžadovat jenom v Function App|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace zabezpečení by měla existovat výstraha protokolu aktivit (Microsoft. Security/securitySolutions/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace zabezpečení (Microsoft. Security/securitySolutions/Write) by měla existovat výstraha protokolu aktivit.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv na zásady: mělo by se povolit zabezpečený přenos do účtů úložiště.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v účtech Batch.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: v předplatném by mělo být povoleno automatické zřizování agenta Log Analytics.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást webové aplikace.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: FTPS by měl být vyžadován ve vaší webové aplikaci|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro servery.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: odběry by měly mít kontaktní e-mailovou adresu pro problémy se zabezpečením.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: veřejný přístup k účtu úložiště by neměl být povolený.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro Kubernetes.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: omezování připojení by mělo být povolené pro PostgreSQL databázové servery.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: Zajistěte, aby webová aplikace měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním k zápisu by se měly z vašeho předplatného odebrat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním ke čtení by se měly z vašeho předplatného odebrat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: mělo by se povolit Azure Defender pro SQL servery na počítačích.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měla by být povolená e-mailová oznámení pro výstrahy s vysokou závažností.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: účet úložiště by měl používat klíč spravovaný zákazníkem pro šifrování.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást webové aplikace.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást aplikace Function App.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' verze PHP ' je nejnovější, pokud se používá jako součást webové aplikace.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást aplikace API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené Azure Defender pro Azure SQL Database servery.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v centru událostí.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: aktualizace systému by se měly nainstalovat na vaše počítače.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást aplikace API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv na zásady: servery SQL by měly být nakonfigurované s uchováním dat v závislosti na 90 dnech nebo vyšším.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP Version ' je nejnovější, pokud se používá ke spuštění webové aplikace|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: v aplikaci API by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: u vašeho předplatného by měla být povolená možnost MFA s oprávněními k zápisu.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: ověřování by mělo být povolené ve vaší webové aplikaci.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: v tajných klíčích musí být nastavené datum vypršení platnosti.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP verze ' je nejnovější, pokud se používá ke spuštění aplikace API.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: v aplikaci API by se měla vyžadovat jenom FTPS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást aplikace Function App.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: webová aplikace by měla být přístupná jen přes protokol HTTPS|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: mělo by se povolit auditování na SQL serveru.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: vícefaktorové ověřování by mělo být povolené u účtů s oprávněním vlastníka u vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: rozšířené zabezpečení dat by mělo být na vašich serverech SQL povolené.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: rozšířené zabezpečení dat by mělo být povolené na spravované instanci SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv na zásady: Role-Based Access Control (RBAC) by se mělo používat pro služby Kubernetes Services.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: monitorovat chybějící Endpoint Protection v Azure Security Center|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly ve vyhledávacích službách.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v App Services.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy (Microsoft. Network/networkSecurityGroups/Delete) by měla existovat výstraha protokolu aktivit.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy by měla existovat výstraha protokolu aktivit (Microsoft. Network/networkSecurityGroups/securityRules/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy (Microsoft. Network/networkSecurityGroups/securityRules/Write) by měla existovat výstraha protokolu aktivit.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy (Microsoft. Network/networkSecurityGroups/Write) by měla existovat výstraha protokolu aktivit.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace správy by měla existovat výstraha protokolu aktivit (Microsoft. SQL/servery/firewallRules/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: pro konkrétní operace správy by měla existovat výstraha protokolu aktivit (Microsoft. SQL/servery/firewallRules/Write).|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měli byste nainstalovat jenom schválená rozšíření virtuálních počítačů.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měla by být povolený Azure Defender pro Registry kontejnerů.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: ve vaší aplikaci API by se měla používat spravovaná identita.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: v aplikaci API by mělo být povolené ověřování.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace zásad by měla existovat výstraha protokolu aktivit (Microsoft. Authorization/policyAssignments/Delete).|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro konkrétní operace zásad by měla existovat výstraha protokolu aktivit (Microsoft. Authorization/policyAssignments/Write).|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: ve vaší aplikaci Function app by mělo být povolené ověřování|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v Data Lake Analytics.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: účty úložiště by měly umožňovat přístup z důvěryhodných služeb Microsoftu|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v Key Vault.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro databázové servery PostgreSQL by mělo být povoleno připojení SSL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP Version ' je nejnovější, pokud se používá ke spuštění aplikace Function App|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: vícefaktorové ověřování by mělo být povolené u účtů s oprávněním ke čtení u vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv na zásadu: přístup k protokolu RDP z Internetu by měl být zablokovaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro databázové servery MySQL by mělo být povoleno připojení SSL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv zásad: Zajistěte, aby aplikace Function App měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: kontrolní body protokolu by měly být povolené pro databázové servery PostgreSQL|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: pro databázové servery PostgreSQL by mělo být povoleno připojení protokolu.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: odpojení by se mělo protokolovat pro databázové servery PostgreSQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Vliv na zásady: posouzení ohrožení zabezpečení by mělo být povoleno na vašich serverech SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: ve vaší webové aplikaci by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v Service Bus.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: měly by být povolené diagnostické protokoly v Azure Stream Analytics.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: v Function App by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Efekt pro zásady: účet úložiště obsahující kontejner s protokoly aktivit musí být zašifrovaný pomocí BYOK.|Další informace o účincích najdete na [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Zahrnout clustery AKS při auditování, jestli jsou povolené diagnostické protokoly virtuálních počítačů s podporou škálování||
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Nejnovější verze Java pro App Services|Nejnovější podporovaná verze Java pro App Services|
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Nejnovější verze Pythonu pro Linux pro App Services|Nejnovější podporovaná verze Pythonu pro App Services|
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Seznam oblastí, kde by měla být povolena Network Watcher|Úplný seznam oblastí zobrazíte spuštěním příkazu PowerShellu Get-AzLocation|
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Nejnovější verze PHP pro App Services|Nejnovější podporovaná verze PHP pro App Services|
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Požadovaná doba uchování (dny) pro protokoly prostředků|Další informace o protokolech prostředků najdete na [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md) |
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Název skupiny prostředků pro Network Watcher|Název skupiny prostředků, ve které se nachází sledovací procesy sítě|
|CI Microsoft Azure Foundation – srovnávací testy v 1.3.0|Přiřazení zásad|Požadované nastavení auditování pro servery SQL||

## <a name="next-steps"></a>Další kroky

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)