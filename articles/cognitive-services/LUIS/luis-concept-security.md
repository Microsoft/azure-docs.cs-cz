---
title: Pochopení přístup k aplikacím LEOŠ - Azure | Microsoft Docs
description: Zjistěte, jak pro přístup k LEOŠ vytváření obsahu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 44380e12e6d095e8d40675af0b6b2fddc5e4c4e9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264263"
---
# <a name="authoring-and-endpoint-user-access"></a>Vytváření obsahu a koncový bod přístupu uživatele
Vytváření přístup je k dispozici pro vlastníky a spolupracovníky. Pro privátní aplikaci je k dispozici pro vlastníky a spolupracovníci přístup koncový bod. Pro veřejného app přístup koncový bod je k dispozici všem uživatelům, které má svůj vlastní účet LEOŠ a ID veřejné aplikace. 

## <a name="access-to-authoring"></a>Přístup k vytváření obsahu
Přístup k aplikaci [LEOŠ] [ LUIS] webu nebo [vytváření rozhraní API](https://aka.ms/luis-authoring-apis) řídí vlastník aplikace. 

Vlastník a všechny spolupracovníci mít přístup k vytváření aplikace. 

|Vytváření přístupu zahrnuje|Poznámky|
|--|--|
|Přidání nebo odebrání koncového bodu klíče||
|Export verze||
|Koncový bod protokoly exportu||
|Import verze||
|Zveřejnit aplikaci|Pokud aplikace je veřejný, každý, kdo má vytváření nebo koncový bod klíč dotazu aplikace.|
|Upravit modelu|
|Publikování|
|Zkontrolujte utterances koncový bod pro [active učení](label-suggested-utterances.md)|
|Trénování|

## <a name="access-to-endpoint"></a>Přístup ke koncovému bodu
Přístup ke koncovému bodu dotazu LEOŠ řídí **veřejné** nastavení aplikace na **nastavení** stránky. Dotaz privátní aplikace koncového bodu se kontroluje na autorizovaného klíče se zbývajícími kvóty přístupů. Dotaz koncový bod veřejné aplikace má také zajistit klíčem koncového bodu (v kdo je provádění dotazu) které se také kontroluje pro zbývající kvóty přístupů. 

Klíč koncového bodu je předán buď v řetězci dotazu požadavku GET nebo záhlaví v příspěvku požadavku.

![Sada aplikaci veřejné](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Zabezpečení koncového bodu privátní aplikace
Koncový bod privátní aplikace je k dispozici následující pouze:

|Klíč a uživatele|Vysvětlení|
|--|--|--|
|Klíč pro tvorbu vlastníka| Až koncový bod 1 000 přístupů|
|Vytváření klíčů spolupracovníci.| Až koncový bod 1 000 přístupů|
|Koncový bod klíče přidají **[publikovat](publishapp.md)** stránky|Vlastníka a spolupracovníci můžete přidat kódy pro koncový bod|

Jiné vytváření nebo koncový bod klíče měly **žádné** přístup.

### <a name="public-app-endpoint-access"></a>Přístup k koncový bod veřejné aplikaci
Konfigurace aplikace jako **veřejné** na **nastavení** stránky aplikace. Jakmile aplikace je nakonfigurovaný jako veřejné, _žádné_ platný LEOŠ vytváření klíč nebo klíč koncového bodu LEOŠ můžete dotazovat aplikace, tak dlouho, dokud kód nepoužili kvótu celý koncový bod.

Uživatel, který není vlastníkem nebo spolupracovník, přístup jenom k veřejného app-li zadány ID aplikace. LEOŠ nemá veřejné _trhu_ nebo jiný způsob, jak hledat veřejného app.  

## <a name="microsoft-user-accounts"></a>Uživatelské účty Microsoft
Autoři a spolupracovníci můžete přidat klíče k LEOŠ na stránce publikovat. Uživatelský účet Microsoft, který vytvoří klíč LEOŠ na portálu Azure musí vlastník aplikace nebo aplikaci spolupracovníka. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Zabezpečení koncového bodu 
Můžete řídit, kdo může zobrazit svůj klíč pro koncový bod LEOŠ voláním v prostředí serveru na server. Pokud používáte LEOŠ z robotu, je už zabezpečené připojení mezi robota a LEOŠ. Koncový bod LEOŠ při volání přímo, měli byste vytvořit rozhraní API na straně serveru (například Azure [funkce](https://azure.microsoft.com/services/functions/)) s řízený přístup (například [AAD](https://azure.microsoft.com/services/active-directory/)). Když je volána rozhraní API na straně serveru a ověřování a autorizace je ověřeno, předejte volání k LEOŠ. Při této strategie není zabránit útokům man-in-the-middle, zastírá váš koncový bod z vašich uživatelů, umožňuje sledovat přístup a umožňuje přidat protokolování odpovědi koncového bodu (jako například [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Dodržování předpisů pro zabezpečení
LEOŠ úspěšně ISO 27001: 2013 a audit 27018:2014 ISO s NULOVÉ při nedodržení požadavků (zjištění) v sestavě auditu. Kromě toho LEOŠ také získat certifikační HVĚZDIČKY CSA s nejvyšší možné Gold ukrývá pro funkce vyhodnocování vyspělosti. Azure je poskytovatele služeb veřejného cloudu pouze hlavní získat této certifikační. Další podrobnosti, můžete najít LEOŠ součástí příkaz aktualizované oboru v Azure hlavní [dodržování předpisů přehled](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) dokumentu, který se odkazuje na [Centrum zabezpečení](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO stránky.  

## <a name="next-steps"></a>Další postup

V tématu [osvědčené postupy](luis-concept-best-practices.md) na další informace o použití tříd Intent a entity pro nejlepší předpovědi.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website