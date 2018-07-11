---
title: Principy přístupu k aplikacím LUIS – Azure | Dokumentace Microsoftu
description: Zjistěte, jak získat přístup k vytváření LUIS.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 90b53bf1334a68fcea813653a7f6d28f362fbd2e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950644"
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
Řídí přístup ke koncovému bodu k dotazování služby LUIS **veřejné** nastavení aplikace **nastavení** stránky. Dotaz soukromých aplikací koncový bod se kontroluje u autorizovaného klíče s zbývající kvóta přístupů. Dotaz koncový bod veřejné aplikace má také poskytnout klíčem koncový bod (z kdo provádí dotaz) která je také kontroluje zbývající kvóta přístupů. 

Klíč koncového bodu se předává v řetězci dotazu požadavku GET nebo požádat o záhlaví příspěvku.

![Sada aplikací na veřejnou](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Zabezpečení koncových bodů privátní aplikaci
Koncový bod soukromých aplikací je pouze k dispozici pro následující:

|Klíč a uživatele|Vysvětlení|
|--|--|--|
|Klíč pro tvorbu vlastníka| Až 1000 koncový bod přístupů|
|Pro vytváření klíčů se spolupracovníky| Až 1000 koncový bod přístupů|
|Přidat z klíče koncového bodu **[publikovat](luis-how-to-publish-app.md)** stránky|Vlastníka a spolupracovníky můžete přidat klíče koncového bodu|

Další vytváření nebo koncový bod klíče mají **žádné** přístup.

### <a name="public-app-endpoint-access"></a>Přístup koncového bodu aplikace z veřejného app
Konfigurace aplikace jako **veřejné** na **nastavení** stránky aplikace. Jakmile aplikace je nakonfigurovaná jako veřejné, _jakékoli_ platný LUIS vytváření klíč nebo klíč koncového bodu služby LUIS můžete dotazovat vaší aplikace, tak dlouho, dokud klíč nebyl použit kvóty celý koncový bod.

Uživatel, který není vlastníkem nebo spolupracovník, můžete pouze přistupovat veřejné aplikace v případě, že zadané ID aplikace. Služba LUIS nemá veřejnou _trhu_ nebo jiný způsob, jak vyhledat aplikace z veřejného app.  

## <a name="microsoft-user-accounts"></a>Uživatelské účty Microsoft
Autoři a spolupracovníky můžete přidat klíče k LUIS na stránce publikovat. Uživatelský účet Microsoft, který vytvoří klíč LUIS na webu Azure Portal, musí vlastník aplikace nebo spolupracovníka aplikace. 

Zobrazit [uživatel tenanta Azure Active Directory](luis-how-to-account-settings.md#azure-active-directory-tenant-user) získat další informace o uživatelských účtů Active Directory. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Zabezpečení koncového bodu 
Můžete řídit, kdo uvidí vaše klíče koncového bodu služby LUIS voláním v prostředí na serveru. Pokud používáte LUIS z robota, je již zabezpečené připojení mezi robotů a LUIS. Pokud koncový bod služby LUIS se volat přímo, měli byste vytvořit rozhraní API na straně serveru (jako je Azure [funkce](https://azure.microsoft.com/services/functions/)) s řízený přístup (například [AAD](https://azure.microsoft.com/services/active-directory/)). Pokud se volá rozhraní API na straně serveru a ověřování a autorizace je ověřeno, předejte volání k LUIS. Při použití této strategie není útokům man-in-the-middle, zastírá váš koncový bod od uživatelů, umožňuje sledovat přístup a slouží k přidání koncového bodu odpovědi protokolování (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Dodržování předpisů zabezpečení
Služba LUIS se úspěšně dokončila, ISO 27001: 2013 a 27018:2014 auditu podle standardu ISO s NULOVOU nedodržení (výsledky) v sestavě auditu. Kromě toho LUIS také získané certifikace CSA STAR s nejvyšší možné zlaté ocenění pro posouzení vyspělosti funkce. Azure je jenom významnější veřejná Cloudová poskytovatel služeb si tohoto certifikátu. Další podrobnosti můžete najít LUIS součástí příkazu aktualizované oboru v Azure hlavní [přehled dodržování předpisů](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) dokument, který se odkazuje na [Centrum](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO stránky.  

## <a name="next-steps"></a>Další postup

Zobrazit [osvědčené postupy](luis-concept-best-practices.md) na další informace o použití záměry a entity, pro nejlepší předpovědi.