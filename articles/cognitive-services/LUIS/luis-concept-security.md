---
title: Zabezpečení při spolupráci – LUIS
titleSuffix: Azure Cognitive Services
description: Vytváření přístup je k dispozici pro vlastníky a spolupracovníky. Privátní aplikaci je k dispozici pro vlastníky a spolupracovníci přístup koncového bodu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 25f6b675846f028b4ba9d49cce9e075bc79eece9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560664"
---
# <a name="authoring-and-endpoint-user-access"></a>Vytváření a koncového bodu přístupu uživatelů
Vytváření přístup je k dispozici pro vlastníky a spolupracovníky. Privátní aplikaci je k dispozici pro vlastníky a spolupracovníci přístup koncového bodu. U veřejné aplikace je přístup k koncovým službám dostupný všem, kdo má vlastní prostředek pro [rozpoznávání](../cognitive-services-apis-create-account.md) Azure nebo prostředek [Luis](luis-how-to-azure-subscription.md#create-prediction-endpoint-runtime-resource-in-the-azure-portal) a má ID veřejné aplikace. 

## <a name="access-to-authoring"></a>Přístup k vytváření obsahu
Přístup k aplikaci z [LUIS](luis-reference-regions.md#luis-website) webu nebo [rozhraní API pro vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) řídí vlastník aplikace. 

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
|Zkontrolujte projevy koncový bod pro [aktivní učení](luis-how-to-review-endpoint-utterances.md)|
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
|--|--|
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

## <a name="securing-the-endpoint"></a>Zabezpečení koncového bodu 

Můžete řídit, kdo uvidí vaše klíče koncového bodu služby LUIS voláním v prostředí na serveru. Pokud používáte LUIS z robota, je již zabezpečené připojení mezi robotů a LUIS. Pokud koncový bod služby LUIS se volat přímo, měli byste vytvořit rozhraní API na straně serveru (jako je Azure [funkce](https://azure.microsoft.com/services/functions/)) s řízený přístup (například [AAD](https://azure.microsoft.com/services/active-directory/)). Pokud se volá rozhraní API na straně serveru a ověřování a autorizace je ověřeno, předejte volání k LUIS. Při použití této strategie není útokům man-in-the-middle, zastírá váš koncový bod od uživatelů, umožňuje sledovat přístup a slouží k přidání koncového bodu odpovědi protokolování (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Dodržování předpisů zabezpečení
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>Další postup

Zobrazit [osvědčené postupy](luis-concept-best-practices.md) na další informace o použití záměry a entity, pro nejlepší předpovědi.
