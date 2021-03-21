---
title: Přidání poskytovatele identity – Azure Active Directory B2C | Microsoft Docs
description: Naučte se, jak přidat poskytovatele identity do tenanta Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 03/03/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: dbb2aeefc22fae4a3c83956dd7681b49fc435500
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033992"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Přidání poskytovatele identity do tenanta Azure Active Directory B2C

Můžete nakonfigurovat Azure AD B2C, aby se uživatelé mohli přihlašovat k aplikaci s přihlašovacími údaji od externích poskytovatelů sociálních sítí nebo podnikových identit (IdP). Azure AD B2C podporuje externí zprostředkovatele identity, jako je Facebook, účet Microsoft, Google, Twitter a jakýkoli poskytovatel identity, který podporuje OAuth 1,0, OAuth 2,0, OpenID Connect a protokol SAML.

S externím federačním zprostředkovatelem identity můžete svým spotřebitelům nabídnout možnost se přihlásit pomocí svých stávajících sociálních nebo podnikových účtů, aniž byste museli vytvořit nový účet jenom pro vaši aplikaci.

Na přihlašovací stránce nebo na přihlašovací stránce Azure AD B2C prezentuje seznam externích zprostředkovatelů identity, které může uživatel zvolit pro přihlášení. Jakmile vyberete jednoho z externích zprostředkovatelů identity, přijímají se (přesměrované) na web vybraného poskytovatele, aby se dokončil proces přihlašování. Jakmile se uživatel úspěšně přihlásí, vrátí se Azure AD B2C pro ověřování účtu ve vaší aplikaci.

![Příklad mobilního přihlášení pomocí účtu sociální sítě (Facebook)](media/add-identity-provider/external-idp.png)

Můžete přidat zprostředkovatele identity, které jsou podporovány aplikací Azure Active Directory B2C (Azure AD B2C), do [toků uživatelů](user-flow-overview.md) pomocí Azure Portal. Do [vlastních zásad](custom-policy-get-started.md)můžete také přidat poskytovatele identity.

## <a name="select-an-identity-provider"></a>Vybrat zprostředkovatele identity

Ve svých aplikacích obvykle používáte pouze jednoho poskytovatele identity, ale máte možnost přidat další. V níže uvedených článcích se dozvíte, jak vytvořit aplikaci poskytovatele identity, přidat zprostředkovatele identity k vašemu tenantovi a přidat poskytovatele identity do toku uživatele nebo vlastní zásady.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Apple](identity-provider-apple-id.md)
* [Azure AD (jeden tenant)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (více tenantů)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [Facebook](identity-provider-facebook.md)
* [Zprostředkovatelé obecných identit](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Účet Microsoft](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (protokol SAML)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
