---
title: Azure Active Directory nejčastějších dotazech k certifikátům správy aplikací
description: Přečtěte si odpovědi na nejčastější dotazy týkající se správy certifikátů pro aplikace pomocí Azure Active Directory jako zprostředkovatele identity (IdP).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803662"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Nejčastější dotazy k certifikátům správy aplikací pro Azure Active Directory (Azure AD)

Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se správy certifikátů pro aplikace pomocí služby Azure Active Directory (Azure AD) jako zprostředkovatele identity (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Existuje způsob, jak vygenerovat seznam certifikátů Podepisování SAML, které vyprší?

Pomocí [skriptů PowerShellu](app-management-powershell-samples.md)můžete exportovat všechny registrace aplikací s vypršením tajných klíčů, certifikátů a jejich vlastníků pro zadané aplikace z vašeho adresáře v souboru CSV. 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Kde najdu informace o tom, co brzy vyprší platnost certifikátů při obnovení?

Postup najdete [tady](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Jak si můžu přizpůsobit datum vypršení platnosti certifikátů vydaných službou Azure AD?

Ve výchozím nastavení Azure AD nakonfiguruje platnost certifikátu, který po třech letech vytvoří automaticky během konfigurace jednotného přihlašování SAML. Vzhledem k tomu, že po uložení nemůžete Datum certifikátu změnit, je nutné vytvořit nový certifikát. Postup, jak to udělat, najdete v tématu [přizpůsobení data vypršení platnosti federačního certifikátu a jeho posunutí na nový certifikát](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Jak můžu automatizovat oznámení o vypršení platnosti certifikátů?

Azure AD pošle e-mailové oznámení 60, 30 a 7 dní před vypršením platnosti certifikátu SAML. Pro příjem oznámení můžete přidat více než jednu e-mailovou adresu. 

> [!NOTE]
> Do seznamu oznámení můžete přidat až 5 e-mailových adres (včetně e-mailové adresy správce, který aplikaci přidal). Pokud potřebujete, aby byli uživatelé upozorněni, použijte distribuční seznam e-maily. 

Pokud chcete zadat e-maily, na které chcete odesílat oznámení, přečtěte si téma [Přidání e-mailových oznámení adres pro vypršení platnosti certifikátu](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

K dispozici není žádná možnost pro úpravu ani přizpůsobení těchto e-mailových oznámení přijatých z `aadnotification@microsoft.com` . Můžete ale exportovat registrace aplikací s platností tajných klíčů a certifikátů prostřednictvím [skriptů PowerShellu](app-management-powershell-samples.md).

## <a name="who-can-update-the-certificates"></a>Kdo může aktualizovat certifikáty?

Vlastník aplikace nebo globálního správce nebo správce aplikace může certifikáty aktualizovat prostřednictvím Azure Portal uživatelského rozhraní, PowerShellu nebo Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Potřebuji další podrobnosti o možnostech podepisování certifikátů.

Ve službě Azure AD můžete nastavit možnosti podepisování certifikátů a algoritmus podepisování certifikátů. Další informace najdete v tématu [Pokročilé možnosti podepisování certifikátů tokenů SAML pro aplikace Azure AD](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Potřebuji nahradit certifikát pro aplikace Azure Proxy aplikací služby AD a potřebujete další pokyny.

Pokud chcete nahradit certifikáty pro aplikace Azure Proxy aplikací služby AD, přečtěte si téma [PowerShell Sample-nahraďte Certificate v App proxy Applications](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Návody spravovat certifikáty pro vlastní domény ve službě Azure Proxy aplikací služby AD?

Pokud chcete nakonfigurovat místní aplikaci tak, aby používala vlastní doménu, budete potřebovat ověřenou Azure Active Directory vlastní doménu, certifikát PFX pro vlastní doménu a místní aplikaci, která se má nakonfigurovat. Další informace najdete v tématu [vlastní domény v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Potřebuji aktualizovat podpisový certifikát tokenu na straně aplikace. Kde se dá dostat na straně Azure AD?

Certifikát SAML X. 509 můžete obnovit v tématu certifikát pro [podpis SAML](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Co je výměna podpisových klíčů Azure AD?

Další podrobnosti najdete [tady](../develop/active-directory-signing-key-rollover.md). 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Návody obnovit šifrovací certifikát tokenu aplikace?

Postup obnovení šifrovacího certifikátu tokenu aplikace najdete v tématu [obnovení certifikátu šifrování tokenu pro podnikovou aplikaci](howto-saml-token-encryption.md). 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Návody obnovit podpisový certifikát tokenu aplikace?

Chcete-li obnovit podpisový certifikát tokenu aplikace, přečtěte si téma [obnovení podpisového certifikátu tokenu pro podnikovou aplikaci](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Návody aktualizovat službu Azure AD po změně federačních certifikátů?

Pokud chcete aktualizovat službu Azure AD po změně federačních certifikátů, přečtěte si téma [obnovení federačních certifikátů pro Microsoft 365 a Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
