---
title: Ukázka podrobného plánu na úrovni DoD. 5
description: Nasaďte kroky pro ukázku podrobného plánu na úrovni DoD – 5 včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 6136136eef5d405ae0849e5ce8c8faede138fb00
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034896"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>Nasaďte ukázku podrobného plánu na úroveň dopadu DoD. 5

Chcete-li nasadit ukázkovou část podrobného plánu ochrany předních plánů Azure (DoD IL5), je nutné provést následující kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte ukázku plán **dopadu dod. 5** a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázky pro 5. plán úrovně dopadu dod.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Projděte si seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

## <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s ovládacími prvky o dopadu na dopad 5.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Zadejte **poznámky ke změnám** , jako je například první verze publikovaná z ukázky DoD IL5 Details Sample. Na konci stránky pak vyberte **Publikovat**.

## <a name="assign-the-sample-copy"></a>Přiřazení ukázkové kopie

Po úspěšném **publikování** kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku zadáte parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán**.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každé z nich pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Podle potřeby ho změňte nebo ponechte.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice** podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Zamknout přiřazení

     Vyberte nastavení uzamčení podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte **Přiřadit** v dolní části stránky. Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobného plánu jsou **bezplatné**. U prostředků Azure se [cena stanovuje podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktů podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|DoD Impact Level 5|Přiřazení zásad|Seznam uživatelů, které musí být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam uživatelů, kteří by měli být zahrnutí do místní skupiny Administrators; Např.: Správce; myUser1; myUser2|
|DoD Impact Level 5|Přiřazení zásad|Seznam uživatelů vyloučených ze skupiny správců virtuálních počítačů s Windows|Středníkem oddělený seznam uživatelů, kteří by měli být vyloučení v místní skupině Administrators; Např.: Správce; myUser1; myUser2|
|DoD Impact Level 5|Přiřazení zásad|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly||
|DoD Impact Level 5|Přiřazení zásad|ID pracovního prostoru Log Analytics pro vytváření sestav agenta virtuálního počítače|ID (GUID) Log Analytics pracovního prostoru, kde by se měly agenti virtuálních počítačů hlásit|
|DoD Impact Level 5|Přiřazení zásad|Seznam oblastí, kde by měla být povolena Network Watcher|Úplný seznam oblastí získáte pomocí Get-AzLocation,|
|DoD Impact Level 5|Přiřazení zásad|Minimální verze protokolu TLS pro webové servery Windows|Minimální verze protokolu TLS, která se má povolit na webových serverech Windows|
|DoD Impact Level 5|Přiřazení zásad|Nejnovější verze PHP|Nejnovější podporovaná verze PHP pro App Services|
|DoD Impact Level 5|Přiřazení zásad|Nejnovější verze Java|Nejnovější podporovaná verze Java pro App Services|
|DoD Impact Level 5|Přiřazení zásad|Nejnovější verze Windows Pythonu|Nejnovější podporovaná verze Pythonu pro App Services|
|DoD Impact Level 5|Přiřazení zásad|Nejnovější verze Pythonu pro Linux|Nejnovější podporovaná verze Pythonu pro App Services|
|DoD Impact Level 5|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů s Windows, které podporují agenta Log Analytics pro přidání do oboru auditování.|Seznam obrázků oddělených středníkem; Příklad:/subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage|
|DoD Impact Level 5|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů se systémem Linux, které podporují agenta Log Analytics pro přidání do oboru auditování|Seznam obrázků oddělených středníkem; Příklad:/subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: k vašemu předplatnému by měl být přiřazený víc než jeden vlastník.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: na virtuálních počítačích by se mělo použít šifrování disku|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: měla by být povolená e-mailová oznámení vlastníkovi předplatného s vysokou závažností.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být pro aplikace Function App vypnuté.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že je verze .NET Framework nejnovější, pokud se používá jako součást Function App|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: měla by být povolená transparentní šifrování dat na databázích SQL.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: vyhodnocení ohrožení zabezpečení by mělo být povolené na spravovaných instancích SQL.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' verze PHP ' je nejnovější, pokud se používá jako součást aplikace API.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: správce Azure Active Directory by měl zřídit pro SQL servery.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: řešení Endpoint Protection by se mělo nainstalovat na Virtual Machine Scale Sets|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Auditovat neomezený síťový přístup k účtům úložiště|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Rozšířená nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv na zásady: Oprava chyb v konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by měla být opravena|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv na zásady: mělo by se povolit zabezpečený přenos do účtů úložiště.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv zásad: měla by být povolená geograficky redundantní záloha pro Azure Database for PostgreSQL|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást webové aplikace.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: pro vaše předplatné by se měla určit maximálně 3 vlastníci.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: pro vaše předplatné by se měla zadat e-mailová adresa kontaktu pro zabezpečení.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním k zápisu by se měly z vašeho předplatného odebrat.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním ke čtení by se měly z vašeho předplatného odebrat.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: zastaralé účty by měly být odebrány z vašeho předplatného|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Function App by měl být dostupný jenom přes HTTPS|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást webové aplikace.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást aplikace Function App.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' verze PHP ' je nejnovější, pokud se používá jako součást webové aplikace.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Python Version ' je nejnovější, pokud se používá jako součást aplikace API.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv na zásady: ohrožení zabezpečení by se měla opravit řešením posouzení ohrožení zabezpečení.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv zásad: měla by být povolená geograficky redundantní záloha pro Azure Database for MySQL|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že je verze .NET Framework nejnovější, pokud se používá jako součást webové aplikace.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: aktualizace systému by se měly nainstalovat na vaše počítače.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást aplikace API.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP Version ' je nejnovější, pokud se používá ke spuštění webové aplikace|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: v aplikaci API by se měla použít nejnovější verze TLS.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: u vašeho předplatného by měla být povolená možnost MFA s oprávněními k zápisu.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Rozšířená nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP verze ' je nejnovější, pokud se používá ke spuštění aplikace API.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: rozšíření Microsoft IaaSAntimalware Extension by mělo být nasazeno na serverech Windows|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' Java Version ' je nejnovější, pokud se používá jako součást aplikace Function App.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: přístup přes internetový koncový bod by měl být omezený.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: měla by být vybraná cenová úroveň Security Center Standard.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: použití auditu pro vlastní pravidla RBAC|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: webová aplikace by měla být přístupná jen přes protokol HTTPS|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: mělo by se povolit auditování na SQL serveru.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: měla by být povolená DDoS Protection Standard.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: vícefaktorové ověřování by mělo být povolené u účtů s oprávněním vlastníka u vašeho předplatného.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' verze PHP ' je nejnovější, pokud se používá jako součást aplikace Function App.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: rozšířené zabezpečení dat by mělo být na vašich serverech SQL povolené.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: rozšířené zabezpečení dat by mělo být povolené na spravovaných instancích SQL.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: e-mailová oznámení správcům a vlastníkům předplatného by měla být povolená v rozšířených nastaveních zabezpečení dat spravované instance SQL|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: monitorovat chybějící Endpoint Protection v Azure Security Center|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv zásad: na virtuálních počítačích by se mělo použít řízení přístupu k síti za běhu|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: pro vaše předplatné by se mělo zadat telefonní číslo kontaktu zabezpečení.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: aplikace API by měla být přístupná jen přes protokol HTTPS|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Rozšířené typy ochrany před hrozbami by měly být nastavené na All v nastaveních rozšířené zabezpečení dat spravované instance SQL.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: mělo by být povoleno geograficky redundantní úložiště pro účty úložiště.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že je verze .NET Framework nejnovější, pokud se používá jako součást aplikace API.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: je potřeba nainstalovat aktualizace systému na služby Virtual Machine Scale Sets.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv zásad: e-mailová oznámení správcům a vlastníkům předplatného by se měla povolit v rozšířených nastaveních zabezpečení dat SQL serveru|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být pro webové aplikace vypnuté.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: dlouhodobě geograficky redundantní zálohování by mělo být povolené pro databáze SQL Azure|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv zásad: ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by měla být opravena|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Ujistěte se, že ' HTTP Version ' je nejnovější, pokud se používá ke spuštění aplikace Function App|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: vícefaktorové ověřování by mělo být povolené u účtů s oprávněním ke čtení u vašeho předplatného.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Rozšířené typy ochrany před hrozbami by měly být nastavené na All v nastavení SQL Server Advanced Data Security.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv zásad: ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by měla být opravena|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být vypnuté pro API Apps|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: zastaralé účty s oprávněním vlastníka by měly být odebrány z vašeho předplatného.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv na zásady: posouzení ohrožení zabezpečení by mělo být povoleno na vašich serverech SQL.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: Agent Log Analytics by měl být nainstalovaný Virtual Machine Scale Sets|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: ve vaší webové aplikaci by se měla použít nejnovější verze TLS.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: v Function App by se měla použít nejnovější verze TLS.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Efekt pro zásady: [Preview]: služby Kubernetes by měly být upgradovány na nezranitelnou Kubernetes verzi|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|
|DoD Impact Level 5|Přiřazení zásad|Vliv na zásady: měla by být opravena ohrožení zabezpečení vašich databází SQL.|Azure Policy efekt pro tuto zásadu; Další informace o účincích najdete na https://aka.ms/policyeffects|

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky podrobného plánu o dopadu DoD. 5, najdete v následujících článcích informace o podrobném plánu a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Úroveň dopadu dod. 5 Details – přehled](./index.md) 
>  [Úroveň dopadu dod. 5 – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)