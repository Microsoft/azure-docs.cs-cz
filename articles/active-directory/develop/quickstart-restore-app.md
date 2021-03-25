---
title: 'Postupy: obnovení nebo odebrání nedávno odstraněné aplikace pomocí platformy Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: V tomto postupu se naučíte, jak obnovit nebo trvale odstranit nedávno odstraněnou aplikaci registrovanou na platformě Microsoft identity.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: a5cf8f42bb15d3ce566506d85fac192027a1c69f
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105039007"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Obnovení nebo odebrání nedávno odstraněné aplikace s platformou Microsoft identity
Po odstranění registrace aplikace zůstane aplikace v pozastaveném stavu po dobu 30 dnů. Během tohoto 30denní okna můžete registraci aplikace obnovit spolu se všemi jejími vlastnostmi. Po uplynutí 30 dnů se registrace aplikace nedají obnovit a automaticky se spustí proces trvalého odstranění.  Tato funkce se vztahuje pouze na aplikace přidružené k adresáři.  Není k dispozici pro aplikace z osobní účet Microsoft, které nelze obnovit.

Můžete zobrazit odstraněné aplikace, obnovit odstraněnou aplikaci nebo trvale odstranit aplikaci pomocí Registrace aplikacího prostředí v části Azure Active Directory (Azure AD) v Azure Portal.

Všimněte si, že ani vy ani zákaznická podpora společnosti Microsoft nemůže obnovit trvale odstraněnou aplikaci nebo aplikaci, která byla odstraněna před více než 30 dny.

## <a name="required-permissions"></a>Požadovaná oprávnění
Chcete-li trvale odstranit aplikace, musíte mít jednu z následujících rolí.

- Globální správce

- Správce aplikace

- Správce cloudové aplikace

- Správce hybridní identity

- Vlastník aplikace

Chcete-li obnovit aplikace, musíte mít jednu z následujících rolí.

- Globální správce

- Vlastník aplikace

## <a name="deleted-applications-ui--preview"></a>Uživatelské rozhraní odstraněné aplikace | Tisk

> [!IMPORTANT]
> Funkce uživatelského rozhraní portálu odstraněné aplikace [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

### <a name="view-your-deleted-applications"></a>Zobrazit odstraněné aplikace
Všechny aplikace můžete zobrazit v tichém odstraněném stavu.  Pouze aplikace, které byly odstraněny před méně než 30 dny, lze obnovit.

#### <a name="to-view-your-restorable-applications"></a>Zobrazení aplikací obnovitelné
1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Vyhledejte a vyberte **Azure Active Directory**, vyberte **Registrace aplikací** a pak vyberte kartu **odstraněné aplikace (Preview)** .

    Zkontrolujte seznam aplikací. K obnovení jsou k dispozici pouze aplikace, které byly odstraněny v posledních 30 dnech. Pokud používáte Registrace aplikací Search ve verzi Preview, můžete filtrovat podle sloupce odstranit datum, aby se zobrazily jenom tyto aplikace.

### <a name="restore-a-recently-deleted-application"></a>Obnovení nedávno odstraněné aplikace

Když se registrace aplikace z organizace odstraní, aplikace je v pozastaveném stavu a zachovají se její konfigurace. Při obnovení registrace aplikace se obnoví i její konfigurace.  Pokud ale v **podnikových aplikacích** pro domácího tenanta aplikace existovala nějaká nastavení specifická pro danou organizaci, nebudou se tato zařízení obnovovat.  

Je to proto, že nastavení specifická pro konkrétní organizaci se ukládají v samostatném objektu, který se označuje jako instanční objekt.  Nastavení uchovávaná u instančního objektu zahrnují souhlas s oprávněními a přiřazení uživatelů a skupin pro určitou organizaci. Tyto konfigurace nebudou obnoveny při obnovení aplikace. Další informace naleznete v tématu [Application and Service instanční objekty](app-objects-and-service-principals.md). 


#### <a name="to-restore-an-application"></a>Obnovení aplikace
1. Na kartě **odstraněné aplikace (Preview)** vyhledejte a vyberte jednu z aplikací, které se před méně než 30 dny odstranily.

2. Vyberte **obnovit registraci aplikace**.

### <a name="permanently-delete-an-application"></a>Trvalé odstranění aplikace
Aplikaci můžete z vaší organizace ručně trvale odstranit. Trvale odstraněnou aplikaci nemůžete obnovit vy, jiného správce nebo zákaznická podpora Microsoftu.

#### <a name="to-permanently-delete-an-application"></a>Trvalé odstranění aplikace

1. Na kartě **odstraněné aplikace (Preview)** vyhledejte a vyberte jednu z dostupných aplikací.

2. Vyberte možnost **odstranit trvale**.

3. Přečtěte si text upozornění a vyberte **Ano**.

## <a name="next-steps"></a>Další kroky
Po obnovení nebo trvale odstranění aplikace můžete:

- [Přidání aplikace](quickstart-register-app.md)

- Přečtěte si další informace o [aplikacích a instančních objektech](app-objects-and-service-principals.md) v platformě Microsoft identity.
