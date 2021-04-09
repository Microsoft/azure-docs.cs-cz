---
title: Ukázka s omezeným plánem na Nový Zéland ISM
description: Přehled ukázkového podrobného plánu s omezením na Novém Zélandu ISM Tento ukázkový podrobný plán pomáhá zákazníkům vyhodnotit konkrétní kontroly.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803926"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Ukázka s omezeným plánem na Nový Zéland ISM

Ukázka s omezením na Nový Zéland ISM poskytuje bezpečnostní zábradlí pomocí [Azure Policy](../../policy/overview.md) , které vám pomůžou vyhodnotit konkrétní ruční ovládací prvky [zabezpečení informací v Novém Zélandu](https://www.nzism.gcsb.govt.nz/) . Tento podrobný plán pomůže zákazníkům nasadit základní sadu zásad pro jakoukoli architekturu nasazenou v Azure, která musí implementovat ovládací prvky pro Nový Zéland s omezením na úrovni ISM.

## <a name="control-mapping"></a>Mapování kontrol

[Mapování ovládacího prvku Azure Policy](../../policy/samples/new-zealand-ism.md) poskytuje podrobné informace o definicích zásad zahrnutých v tomto podrobném plánu a o tom, jak se tyto definice zásad mapují k **ovládacím prvkům** v Novém Zélandu zabezpečení informací. Při přiřazení k architektuře jsou prostředky vyhodnocovány Azure Policym při nedodržení předpisů s přiřazenými definicemi zásad. Další informace najdete v tématu [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Nasadit

Pokud chcete nasadit Azure Modrotiskys New Zéland s omezeným plánem, musí být podniknuty tyto kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. Vyhledejte vzorek **s omezením na Novém Zélandu ISM** v části _Další ukázky_ a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázky s omezeným plánem ISM ISM.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Zkontrolujte seznam artefaktů, které jsou zahrnuty v ukázce podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

### <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s omezeními, které mají nové Zélandu ISM.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Poskytněte **poznámky ke změnám** , jako je například "první verze publikovaná z nového podrobného plánu s omezeným přístupem ISM ISM". Na konci stránky pak vyberte **Publikovat**.

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
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Seznam uživatelů, které musí být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam uživatelů, kteří by měli být zahrnutí do místní skupiny Administrators; Např.: Správce; myUser1; myUser2|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Seznam uživatelů, kteří musí být vyloučení ze skupiny správců virtuálních počítačů s Windows|Středníkem oddělený seznam uživatelů, kteří by měli být vyloučení v místní skupině Administrators; Např.: Správce; myUser1; myUser2|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Seznam uživatelů, které skupina správců virtuálních počítačů s Windows smí zahrnovat jenom|Středníkem oddělený seznam všech očekávaných členů místní skupiny Administrators; Např.: Správce; myUser1; myUser2|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|ID pracovního prostoru Log Analytics pro vytváření sestav agenta virtuálního počítače|ID (GUID) Log Analytics pracovního prostoru, kde by se měly agenti virtuálních počítačů hlásit|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: Brána Firewall webových aplikací (WAF) by měla být povolená pro službu Azure front-dveří|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv zásad: nastavení posouzení ohrožení zabezpečení pro SQL Server by měla obsahovat e-mailovou adresu pro příjem sestav kontroly.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: doporučení adaptivního posílení zabezpečení sítě by se měla použít u virtuálních počítačů s přístupem k Internetu.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: k vašemu předplatnému by měl být přiřazený víc než jeden vlastník.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: na virtuálních počítačích by se mělo použít šifrování disku|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být pro aplikace Function App vypnuté.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: Firewall webových aplikací (WAF) by měl používat zadaný režim pro Application Gateway|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Požadavek na režim WAF pro Application Gateway|V Application Gateway službě musí být povolený režim prevence nebo detekce.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: měla by být povolená transparentní šifrování dat na databázích SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: posouzení ohrožení zabezpečení by mělo být povolené na spravované instanci SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Volitelné: seznam vlastních imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru dalších obrázků v galerii pro zásady: Deploy-Configure Dependency agent, který bude povolený na virtuálních počítačích s Windows.|Další informace o konfiguraci hostů najdete na [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: správce Azure Active Directory by měl zřídit pro SQL servery.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: měla by být povolená jenom zabezpečená připojení k mezipaměti Azure cache pro Redis.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: řešení Endpoint Protection by se mělo nainstalovat na Virtual Machine Scale Sets|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Při vyhodnocování zásad zahrnout servery připojené k ARC: Auditovat počítače s Windows chybějící žádné ze zadaných členů ve skupině Administrators|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k oblouku účtovat měsíčně.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Volitelné: seznam vlastních imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru dalších obrázků v galerii pro zásady: [Preview]: Log Analytics agent by měl být povolený pro uvedené image virtuálních počítačů.|Další informace o konfiguraci hostů najdete na [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Volitelné: seznam vlastních imagí virtuálních počítačů, které podporovaly Linux OS pro přidání do oboru dalších obrázků v galerii pro zásady: [Preview]: Log Analytics agent by měl být povolený pro uvedené image virtuálních počítačů.|Další informace o konfiguraci hostů najdete na [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv zásad: účty úložiště by měly omezit síťový přístup|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Volitelné: seznam vlastních imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru dalších obrázků v galerii pro zásady: Deploy-Configure Dependency agent, který bude povolený ve Windows Virtual Machine Scale Sets|Další informace o konfiguraci hostů najdete na [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv na zásady: Oprava chyb v konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by měla být opravena|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Při vyhodnocování zásad zahrnout servery připojené k ARC: Auditovat počítače s Windows, které mají ve skupině Administrators další účty|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k oblouku účtovat měsíčně.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv na zásady: mělo by se povolit zabezpečený přenos do účtů úložiště.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: Firewall webových aplikací (WAF) by měl používat zadaný režim pro službu Azure front-dveří.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Požadavek na režim WAF pro službu Azure front-dveří|U služby front-dveří Azure musí být povolený režim prevence nebo detekce.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: v počítačích by měla být povolená Adaptivní řízení aplikací pro definování bezpečných aplikací.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: pro vaše předplatné by se měla určit maximálně 3 vlastníci.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: [Preview]: přístup k účtu úložiště by měl být zakázaný.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: Firewall webových aplikací (WAF) by měl být povolený pro Application Gateway|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Při vyhodnocování zásad zahrnout servery připojené k ARC: Auditovat webové servery Windows, které nepoužívají zabezpečené komunikační protokoly|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k oblouku účtovat měsíčně.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Minimální verze protokolu TLS pro webové servery Windows|Webové servery Windows s nižšími verzemi TLS budou posouzené jako nevyhovující předpisům.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Volitelné: seznam vlastních imagí virtuálních počítačů, které podporovaly Linux OS pro přidání do oboru dalších obrázků v galerii pro zásady: Agent Log Analytics by měl být povolený ve službě Virtual Machine Scale Sets pro uvedené image virtuálních počítačů.|Další informace o konfiguraci hostů najdete na [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Volitelné: seznam vlastních imagí virtuálních počítačů, které mají podporovaný operační systém Windows pro přidání do oboru dalších obrázků v galerii pro zásady: Agent Log Analytics by měl být povolený ve službě Virtual Machine Scale Sets pro uvedené image virtuálních počítačů.|Další informace o konfiguraci hostů najdete na [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním k zápisu by se měly z vašeho předplatného odebrat.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Při vyhodnocování zásad zahrnout servery připojené k ARC: Auditovat počítače s Windows, které mají zadané členy ve skupině Administrators|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k oblouku účtovat měsíčně.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: zastaralé účty by měly být odebrány z vašeho předplatného|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: Function App by měl být dostupný jenom přes HTTPS|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: předplatná Azure by měla mít profil protokolu aktivit.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly||
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: aktualizace systému by se měly nainstalovat na vaše počítače.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: v aplikaci API by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: u vašeho předplatného by měla být povolená možnost MFA s oprávněními k zápisu.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: rozšíření Microsoft IaaSAntimalware Extension by mělo být nasazeno na serverech Windows|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: webová aplikace by měla být přístupná jen přes protokol HTTPS|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: měla by být povolená Azure DDoS Protection Standard.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: vícefaktorové ověřování by mělo být povolené u účtů s oprávněním vlastníka u vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: rozšířené zabezpečení dat by mělo být na vašich serverech SQL povolené.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: rozšířené zabezpečení dat by mělo být povolené na spravované instanci SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: monitorovat chybějící Endpoint Protection v Azure Security Center|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: protokol aktivit by se měl uchovávat aspoň po dobu jednoho roku.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: aplikace API by měla být přístupná jen přes protokol HTTPS|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv na zásady: Auditovat počítače s Windows, na kterých není zapnutá ochrana před zneužitím v programu Windows Defender|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Při vyhodnocování zásad zahrnout servery připojené k ARC: Auditovat počítače s Windows, na kterých není zapnutá ochrana před zneužitím v programu Windows Defender|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k oblouku účtovat měsíčně.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Stav dodržování předpisů pro počítače se systémem Windows, ve kterých není k dispozici ochrana před zneužitím v programu Windows Defender|Ochrana před zneužitím v programu Windows Defender je dostupná jenom od Windows 10/Windows serveru s aktualizací 1709. Když nastavíte tuto hodnotu na nekompatibilní, zobrazí se počítače se staršími verzemi, na kterých není k dispozici ochrana pomocí programu Windows Defender zneužití Guard (například Windows Server 2012 R2), která nedodržuje předpisy. Když se nastaví hodnota kompatibilní, zobrazí se tyto počítače jako vyhovující předpisům.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: je potřeba nainstalovat aktualizace systému na služby Virtual Machine Scale Sets.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být pro webové aplikace vypnuté.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv zásad: ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by měla být opravena|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: vícefaktorové ověřování by mělo být povolené u účtů s oprávněním ke čtení u vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv zásad: ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by měla být opravena|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: vzdálené ladění by mělo být vypnuté pro API Apps|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: auditujte počítače Linux, které umožňují vzdálená připojení z účtů bez hesel.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Při vyhodnocování zásad zahrnout servery připojené k ARC: Auditovat počítače Linux, které umožňují vzdálená připojení z účtů bez hesel|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k oblouku účtovat měsíčně.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: zastaralé účty s oprávněním vlastníka by měly být odebrány z vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv na zásady: posouzení ohrožení zabezpečení by mělo být povoleno na vašich serverech SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: ve vaší webové aplikaci by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv na zásady: počítače s Windows by měly splňovat požadavky na nastavení zabezpečení Zásady účtů|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vynutili historii hesel pro místní účty virtuálních počítačů s Windows|Určuje omezení opakovaného použití hesla – kolikrát se pro uživatelský účet musí vytvořit nové heslo, aby bylo možné heslo opakovat.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Při vyhodnocování zásad zahrnout servery připojené k ARC: počítače s Windows by měly splňovat požadavky na nastavení zabezpečení Zásady účtů|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k oblouku účtovat měsíčně.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Maximální stáří hesla pro místní účty virtuálních počítačů s Windows|Určuje maximální počet dní, které mohou uplynout, než bude nutné změnit heslo k uživatelskému účtu. Formát hodnoty je dvě celá čísla oddělená čárkou, která označuje rozsah (včetně).|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Minimální stáří hesla pro místní účty virtuálních počítačů s Windows|Určuje minimální počet dní, které musí uplynout, než bude možné změnit heslo k uživatelskému účtu.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Minimální délka hesla pro místní účty virtuálních počítačů s Windows|Určuje minimální počet znaků, které může heslo k uživatelskému účtu obsahovat.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Heslo musí splňovat požadavky na složitost pro místní účty virtuálních počítačů s Windows.|Určuje, jestli musí být heslo uživatelského účtu složité. v případě potřeby nesmí složitá hesla obsahovat část názvu uživatelského účtu nebo úplného názvu. aspoň 6 znaků dlouhé; obsahuje kombinaci velkých a malých písmen, číslic a znaků bez abecedy.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv zásad: virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: audit počítačů se systémem Linux, které mají účty bez hesla|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Při vyhodnocování zásad zahrnout servery připojené k ARC: Auditovat počítače Linux s účty bez hesla|Výběrem možnosti "pravda" souhlasíte s tím, že se má za každý počítač připojený k oblouku účtovat měsíčně.|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: externí účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: v Function App by se měla použít nejnovější verze TLS.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Efekt pro zásady: [Preview]: veškerý internetový provoz by měl být směrován prostřednictvím nasazené Azure Firewall|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nový Zéland s omezením pro ISM|Přiřazení zásad|Vliv na zásady: měla by být opravena ohrožení zabezpečení vašich databází SQL.|Další informace o účincích najdete na [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Další kroky

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)