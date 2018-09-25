---
title: Principy přístupu k aplikacím LUIS
titleSuffix: Azure Cognitive Services
description: Vytváření přístup je k dispozici pro vlastníky a spolupracovníky. Privátní aplikaci je k dispozici pro vlastníky a spolupracovníci přístup koncového bodu. Veřejné aplikace koncový bod přístup je k dispozici pro všechny uživatele, který má svůj vlastní účet služby LUIS a má ID veřejné aplikace.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8a7ecac3776d767160b07f550251c54793926056
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033188"
---
# <a name="authoring-and-endpoint-user-access"></a>Vytváření a koncového bodu přístupu uživatelů
Vytváření přístup je k dispozici pro vlastníky a spolupracovníky. Privátní aplikaci je k dispozici pro vlastníky a spolupracovníci přístup koncového bodu. Veřejné aplikace koncový bod přístup je k dispozici pro všechny uživatele, který má svůj vlastní účet služby LUIS a má ID veřejné aplikace. 

## <a name="access-to-authoring"></a>Přístup k vytváření obsahu
Přístup k aplikaci z [LUIS](luis-reference-regions.md#luis-website) webu nebo [rozhraní API pro vytváření](https://aka.ms/luis-authoring-apis) řídí vlastník aplikace. 

Vlastníka a spolupracovníky všechny mají přístup k vytváření aplikací. 

|Vytváření přístup zahrnuje|Poznámky|
|--|--|
|Přidání nebo odebrání klíče koncového bodu||
|Export verze||
|Exportovat protokoly koncového bodu||
|Importuje se verze||
|Zveřejnit aplikaci|Pokud aplikace je veřejný, kdokoli s klíčem s vytvářením nebo koncový bod aplikace dotazu.|
|Upravit model|
|Publikování|
|Zkontrolujte projevy koncový bod pro [aktivní učení](luis-how-to-review-endoint-utt.md)|
|Trénování|

## <a name="access-to-endpoint"></a>Přístup ke koncovému bodu
Přístup k dotazování na koncový bod je řízen pomocí nastavení na **informací o aplikaci** stránku **spravovat** oddílu. 

![Sada aplikací na veřejnou](./media/luis-concept-security/set-application-as-public.png)

|[Privátního koncového bodu](#private-app-endpoint-security)|[Veřejný koncový bod](#public-app-endpoint-access)|
|:--|:--|
|K dispozici pro vlastníka a spolupracovníky|K dispozici pro vlastníka, spolupracovníky a kdokoli jinak, který zná ID aplikace|

### <a name="private-app-endpoint-security"></a>Zabezpečení koncových bodů privátní aplikaci
Koncový bod soukromých aplikací je pouze k dispozici pro následující:

|Klíč a uživatele|Vysvětlení|
|--|--|--|
|Klíč pro tvorbu vlastníka| Až 1000 koncový bod přístupů|
|Pro vytváření klíčů se spolupracovníky| Až 1000 koncový bod přístupů|
|Libovolná klávesa přiřadil Autor nebo spolupracovníků k LUIS|Založené na úrovni použití klíče|

#### <a name="microsoft-user-accounts"></a>Uživatelské účty Microsoft
Autoři a spolupracovníky můžete přiřadit klíče k privátní aplikaci LUIS. Uživatelský účet Microsoft, který vytvoří klíč LUIS na webu Azure Portal musí být vlastníkem aplikace nebo spolupracovníka aplikace. Nelze přiřadit klíč k privátní aplikaci z jiného účtu Azure.

Zobrazit [uživatel tenanta Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) získat další informace o uživatelských účtů Active Directory. 

### <a name="public-app-endpoint-access"></a>Přístup koncového bodu aplikace z veřejného app
Jakmile aplikace je nakonfigurovaná jako veřejné, _jakékoli_ platný LUIS vytváření klíč nebo klíč koncového bodu služby LUIS můžete dotazovat vaší aplikace, tak dlouho, dokud klíč nebyl použit kvóty celý koncový bod.

Uživatel, který není vlastníkem nebo spolupracovník, můžete pouze přistupovat veřejné aplikace v případě, že zadané ID aplikace. Služba LUIS nemá veřejnou _trhu_ nebo jiný způsob, jak vyhledat aplikace z veřejného app.  

Aplikace z veřejného app je publikována ve všech oblastech, tak, aby uživatel s klíčem služby LUIS prostředků na základě oblasti můžete přistupovat k aplikaci v oblasti podle toho, která souvisí s klíč prostředku.

## <a name="microsoft-user-accounts"></a>Uživatelské účty Microsoft
Autoři a spolupracovníky můžete přidat klíče k LUIS na stránce publikovat. Uživatelský účet Microsoft, který vytvoří klíč LUIS na webu Azure Portal musí být vlastníkem aplikace nebo spolupracovníka aplikace. 

Zobrazit [uživatel tenanta Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) získat další informace o uživatelských účtů Active Directory. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->

## <a name="securing-the-endpoint"></a>Zabezpečení koncového bodu 
Můžete řídit, kdo uvidí vaše klíče koncového bodu služby LUIS voláním v prostředí na serveru. Pokud používáte LUIS z robota, je již zabezpečené připojení mezi robotů a LUIS. Pokud koncový bod služby LUIS se volat přímo, měli byste vytvořit rozhraní API na straně serveru (jako je Azure [funkce](https://azure.microsoft.com/services/functions/)) s řízený přístup (například [AAD](https://azure.microsoft.com/services/active-directory/)). Pokud se volá rozhraní API na straně serveru a ověřování a autorizace je ověřeno, předejte volání k LUIS. Při použití této strategie není útokům man-in-the-middle, zastírá váš koncový bod od uživatelů, umožňuje sledovat přístup a slouží k přidání koncového bodu odpovědi protokolování (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Dodržování předpisů zabezpečení
 
[!include[LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>Další postup

Zobrazit [osvědčené postupy](luis-concept-best-practices.md) na další informace o použití záměry a entity, pro nejlepší předpovědi.
