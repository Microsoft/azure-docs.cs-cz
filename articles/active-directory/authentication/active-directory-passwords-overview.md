---
title: Azure AD samoobslužného resetování hesel přehled | Microsoft Docs
description: Co může Azure AD samoobslužné služby heslo resetovat si pro vaši organizaci?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 77d26d860462b1a82315bf1385bccf62047e3da3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Azure AD samoobslužného resetování hesla pro IT profesionály

S Azure Active Directory (Azure AD) samoobslužné resetování hesla (SSPR) můžete uživatelům resetovat vlastní hesla na své vlastní kdy a kde potřebují. Ve stejnou dobu správci můžou řídit jak získá resetování hesla uživatele. Uživatelé se již muset volat HelpDesk jenom k resetování hesla. Azure AD SSPR zahrnuje:

* **Změna hesla pomocí samoobslužné služby**: uživatel zná heslo, ale chce si ho změnit na něco nové.
* **Resetování hesla pomocí samoobslužné služby**: uživatel se nemůže přihlásit a chce resetovat heslo pomocí jednoho nebo více z následujících metod ověřování ověřené:
   * Odeslala se textová zpráva na mobilní telefon ověřen.
   * Telefonní hovor ověřené telefon mobilní telefon nebo office.
   * Odešlete e-mail ověřené sekundární e-mailový účet.
   * Zodpovědět své bezpečnostní otázky.
* **Odemknutí účtu samoobslužné služby**: uživatel nemůže se přihlásit pomocí hesla a byl uzamčen. Uživatel chce odemknout svůj účet bez zásahu správce pomocí své metody ověřování.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

## <a name="why-choose-azure-ad-sspr"></a>Proč zvolit Azure AD SSPR

Azure AD SSPR vám pomůže:

* **Snížení nákladů** jako pomoc obvykle resetování hesel odbornou podpory účet pro 20 % volání podpory IT organizace. 
* **Vylepšení prostředí pro koncové uživatele** a **snížit počet nápovědy na technickou podporu** tím, že koncoví uživatelé power k řešení problémů své vlastní heslo. Není nutné volat HelpDesk nebo otevřít žádost o podporu.
* **Jednotka mobility** jako uživatelům můžete resetovat vlastní hesla ze kdekoli jsou.
* **Zachovat kontrolu** zásad zabezpečení. Správci mohou nadále vynucovat zásady, které ještě dnes.

Pokud budete připraveni, můžete začít s Azure AD SSPR pomocí našich [rychlý start pokyny](quickstart-sspr.md). Uživatelům můžete rychle poskytovat k umožňuje resetovat jejich hesla.

## <a name="azure-ad-sspr-availability"></a>Azure AD SSPR dostupnosti

Azure AD SSPR, jsou k dispozici ve třech úrovních v závislosti na vaše předplatné:

* **Azure AD volné**: jenom pro Cloud správci můžou resetovat vlastní hesla.
* **Azure AD Basic** nebo jakoukoli **placené předplatné služeb Office 365**: jenom pro Cloud uživatelům můžete resetovat jejich hesla.
* **Azure AD Premium**: všechny uživatele nebo správce, včetně jenom pro cloud, federované, předávací ověřování, nebo uživatelé hash synchronizovat hesla, můžete resetovat jejich hesla. Místních hesel vyžadují zpětný zápis hesla tak, aby byl povolen.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD ceny, smlouvy o úrovni služeb, aktualizace a plán

Další informace o licencování, ceny a budoucí plány naleznete na následující servery:

* [Podrobnosti o cenách služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Ceny Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/)
* [Dohoda o úrovni služeb pro Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure aktualizace](https://azure.microsoft.com/updates/)
* [Plány Azure do budoucna](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Další postup

* Jste připraveni začít pracovat s SSPR? [Nastavení hesla pomocí samoobslužné služby Azure AD resetovat](quickstart-sspr.md).
* Naplánovat úspěšné nasazení SSPR uživatelům pomocí pokynů v našem [zavedení průvodce](howto-sspr-deployment.md).
* [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../active-directory-passwords-reset-register.md)
