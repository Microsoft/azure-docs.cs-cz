---
title: Ověřování a autorizace pro statickou Web Apps Azure
description: Naučte se používat jiné zprostředkovatele autorizace k zabezpečení vaší statické aplikace.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: ab41a336c32a1827c23f4c4619f47dc294a4d2ea
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419282"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Ověřování a autorizace pro službu Azure Static Web Apps ve verzi Preview

Služba Azure static Web Apps zjednodušuje možnosti ověřování tím, že spravuje ověřování s následujícími poskytovateli:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

[Pozvánky](#invitations) specifické pro poskytovatele spojují uživatele s rolemi a autorizovanými uživateli je udělen přístup k [trasám](routes.md) podle pravidel definovaných v _routes.jsv_ souboru.

Všichni poskytovatelé ověřování jsou ve výchozím nastavení povoleni. Chcete-li zakázat poskytovatele ověřování, [Zablokujte přístup](#block-an-authorization-provider) pomocí vlastního pravidla směrování.

Témata týkající se ověřování a autorizace se významně překrývají s koncepty směrování. Nezapomeňte si přečíst [Průvodce směrováním](routes.md) spolu s tímto článkem.

## <a name="roles"></a>Role

Každý uživatel, který přistupuje ke statické webové aplikaci, patří k jedné nebo více rolím.  K dispozici jsou dvě předdefinované role, do kterých uživatelé můžou patřit:

- **anonymní**: všichni uživatelé automaticky patří do _anonymní_ role.
- **ověřeno**: všichni uživatelé, kteří se přihlásili, patří k _ověřené_ roli.

Kromě předdefinovaných rolí můžete vytvořit nové role, přiřazovat je uživatelům přes pozvánky a odkazovat na ně v _routes.jsv_ souboru.

## <a name="role-management"></a>Správa rolí

### <a name="add-a-user-to-a-role"></a>Přidání uživatele k roli

Chcete-li přidat uživatele na web, vygenerujete pozvánky, které vám umožní přidružit uživatele k určitým rolím. Role se definují a udržují v _routes.jsv_ souboru.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Vytvoření pozvánky

Pozvánky jsou specifické pro jednotlivé zprostředkovatele autorizace, takže při výběru zprostředkovatelů, které chcete podporovat, zvažte potřeby vaší aplikace. Někteří poskytovatelé zveřejňují e-mailové adresy uživatele, zatímco jiné poskytují jenom uživatelské jméno webu.

<a name="provider-user-details" id="provider-user-details"></a>

| Zprostředkovatel autorizace | Zpřístupňuje uživatele  |
| ---------------------- | ----------------- |
| Azure Active Directory | e-mailovou adresu     |
| Facebook               | e-mailovou adresu     |
| GitHubu                 | username          |
| Google<sup>1</sup>     | e-mailovou adresu     |
| Twitter                | username          |

1. V [Azure Portal](https://portal.azure.com)přejděte na prostředek statického Web Apps.
1. V části _Nastavení_ klikněte na **Správa rolí**.
1. Klikněte na tlačítko **pozvat** .
1. V seznamu možností vyberte _poskytovatele autorizace_ .
1. Do pole s _podrobnostmi pozvánky_ přidejte buď uživatelské jméno nebo e-mailovou adresu příjemce.
    - V případě GitHubu a Twitteru zadáte uživatelské jméno. Pro všechny ostatní zadejte e-mailovou adresu příjemce.
1. Z rozevíracího seznamu _doména_ vyberte doménu vaší statické lokality.
    - Doména, kterou vyberete, je doména, která se zobrazí v pozvánce. Pokud máte k lokalitě přidruženou vlastní doménu, budete pravděpodobně chtít zvolit vlastní doménu.
1. Do pole _role_ přidejte čárkami oddělený seznam názvů rolí.
1. Zadejte maximální počet hodin, po které má Pozvánka zůstat platná.
    - Maximální možné omezení je 168 hodin, což je 7 dní.
1. Klikněte na tlačítko **Generate** (Vygenerovat).
1. Zkopírujte odkaz z pole _pro pozvání k pozvání_ .
1. Pošlete odkaz na pozvánku na osobu, které udělujete přístup k vaší aplikaci.

Když uživatel klikne na odkaz v rámci pozvánky, zobrazí se výzva, abyste se přihlásili pomocí odpovídajícího účtu. Po úspěšném přihlášení je uživatel přidružen k vybraným rolím.

> [!CAUTION]
> Ujistěte se, že pravidla směrování nejsou v konfliktu s vybranými poskytovateli ověřování. Blokováním zprostředkovatele s pravidlem směrování by si uživatelé nemohli přijmout pozvánky.

### <a name="update-role-assignments"></a>Aktualizovat přiřazení rolí

1. V [Azure Portal](https://portal.azure.com)přejděte na prostředek statického Web Apps.
1. V části _Nastavení_ klikněte na **Správa rolí**.
1. V seznamu klikněte na uživatele.
1. V poli _role_ upravte seznam rolí.
1. Klikněte na tlačítko **aktualizovat** .

### <a name="remove-user"></a>Odebrání uživatele

1. V [Azure Portal](https://portal.azure.com)přejděte na prostředek statického Web Apps.
1. V části _Nastavení_ klikněte na **Správa rolí**.
1. Vyhledejte uživatele v seznamu.
1. Zaškrtněte políčko na řádku uživatele.
1. Klikněte na tlačítko **Odstranit**.

Při odebírání uživatele Pamatujte na následující položky:

1. Odebráním uživatele se zruší platnost svých oprávnění.
1. Šíření po celém světě může trvat několik minut.
1. Pokud se uživatel do aplikace přidá zpátky, [ `userId` změny](user-information.md).

## <a name="remove-personal-identifying-information"></a>Odebrat osobní identifikační údaje

Pokud udělíte souhlas aplikaci jako koncový uživatel, aplikace bude mít přístup k vaší e-mailové adrese nebo uživatelskému jménu v závislosti na zprostředkovateli identity. Po zadání těchto informací se vlastník aplikace rozhodne, jak spravovat osobní identifikační údaje.

Koncoví uživatelé potřebují požádat správce jednotlivých webových aplikací, aby tyto informace odvolali ze svých systémů.

Pokud chcete z platformy Azure static Web Apps odebrat osobní identifikační údaje a zabránit tomu, aby tato informace mohla v budoucích požadavcích poskytovat tyto informace, odešlete žádost pomocí adresy URL:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Pokud chcete zabránit tomu, aby platforma poskytovala tyto informace v budoucích požadavcích na jednotlivé aplikace, odešlete žádost na následující adresu URL:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Systémová složka

Statický Web Apps Azure používá `/.auth` systémovou složku k poskytnutí přístupu k rozhraním API, která se vztahují k autorizaci. Místo vystavení jakékoli trasy ve `/.auth` složce přímo koncovým uživatelům zvažte vytvoření [pravidel směrování](routes.md) pro vytváření popisných adres URL.

## <a name="login"></a>Přihlásit

Pomocí následující tabulky Najděte trasu pro přihlášení specifickou pro konkrétního poskytovatele.

| Zprostředkovatel autorizace | Trasa pro přihlášení             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHubu                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Například pro přihlášení pomocí GitHubu můžete zahrnout přihlašovací odkaz, jako je následující fragment kódu:

```html
<a href="/.auth/login/github">Login</a>
```

Pokud se rozhodnete podporovat více než jednoho poskytovatele, musíte pro každý z nich vystavit odkaz na konkrétního poskytovatele.

[Pravidlo směrování](./configuration.md#routes) můžete použít k mapování výchozího poskytovatele na přívětivou trasu, jako je _/Login_.

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Přesměrování po přihlášení

Pokud chcete, aby se uživatel po přihlášení vrátil na konkrétní stránku, zadejte adresu URL v `post_login_redirect_uri` parametru řetězce dotazu.

## <a name="logout"></a>Odhlásit

`/.auth/logout`Směrování zaznamená uživatele z webu. Můžete přidat odkaz na navigaci na webu, aby se uživatel mohl odhlásit, jak je znázorněno v následujícím příkladu.

```html
<a href="/.auth/logout">Log out</a>
```

[Pravidlo směrování](./configuration.md#routes) můžete použít k mapování popisné trasy, jako je _/logout_.

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Přesměrování po odhlášení

Pokud chcete, aby se uživatel po odhlášení vrátil na konkrétní stránku, zadejte adresu URL v `post_logout_redirect_uri` parametru řetězce dotazu.

## <a name="block-an-authorization-provider"></a>Blokování zprostředkovatele autorizace

Můžete chtít, aby aplikace omezila použití zprostředkovatele autorizace. Například vaše aplikace může chtít standardizovat pouze [poskytovatele, kteří zveřejňují e-mailové adresy](#provider-user-details).

Pokud chcete poskytovatele zablokovat, můžete vytvořit [pravidla směrování](routes.md) , která vrátí 404 pro požadavky na trasu určenou pro blokovaného zprostředkovatele. Chcete-li například omezit Twitter jako poskytovatele, přidejte následující pravidlo směrování.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Omezení

Obecná omezení a omezení najdete v článku věnovaném [kvótám](quotas.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přístup k datům o ověřování a autorizaci uživatelů](user-information.md)

<sup>1</sup> čeká na externí certifikaci.
