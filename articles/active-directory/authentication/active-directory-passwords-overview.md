---
title: Azure AD samoobslužné resetování hesla – přehled | Dokumentace Microsoftu
description: Co můžou služby Azure AD hesla pomocí samoobslužné služby obnovit pro vaši organizaci?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e28918fe9e26221738fe234ad41923c58a6ac260
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049017"
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Samoobslužné resetování hesla Azure AD pro IT profesionály

S Azure Active Directory (Azure AD) samoobslužné resetování hesla (SSPR) můžete uživatelům na jejich vlastní kdy a kde potřebují k resetování hesel. Ve stejnou dobu správci můžou řídit jak získá resetování hesla uživatele. Uživatelé už nemusí zavolat oddělení technické podpory jenom k resetování hesla. Samoobslužné resetování HESLA Azure AD zahrnuje:

* **Změna hesla pomocí samoobslužné služby**: uživatel zná heslo, ale chce změnit na něco nového.
* **Samoobslužné resetování hesla**: uživatel nemůže přihlásit a chce, aby se resetování hesla pomocí jednoho nebo více z následujících metod ověřování ověřené:
   * Odešlete textovou zprávu na ověřené mobilní telefon.
   * Proveďte ověřené telefonu mobilní telefon nebo office telefonního hovoru.
   * Odeslání e-mailu ověřené sekundární e-mailový účet.
   * Zodpovědět své bezpečnostní otázky.
* **Odemknutí účtu samoobslužné**: uživatel nemůže přihlásit pomocí hesla a byl uzamčen. Uživatel chce odemknout svůj účet bez zásahu správce pomocí své metody ověřování.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

## <a name="why-choose-azure-ad-sspr"></a>Proč zvolit službu Azure AD SSPR

Samoobslužné resetování HESLA Azure AD vám umožní:

* **Snížení nákladů na** jako Nápověda obvykle resetování hesla podpory s asistencí účet pro 20 % volání podpory v IT organizaci. 
* **Vylepšení prostředí pro koncové uživatele** a **snížit help desk** tím, že koncoví uživatelé výkonné nástroje pro řešení problémů s jejich vlastní heslo. Není nutné volat HelpDesk nebo žádost o podporu.
* **Jednotka mobility** jako uživatelům můžete resetovat heslo, ať jsou kdekoli.
* **Trvalá kontrola** zásad zabezpečení. Správci mohou nadále vynucovat zásady, které mají ještě dnes.

Pokud jste připraveni, můžete začít s samoobslužného resetování HESLA Azure AD s použitím našich [úvodní doprovodné materiály](quickstart-sspr.md). Můžete rychle uživatelům vaší schopnost resetování vlastních hesel.

## <a name="azure-ad-sspr-availability"></a>Dostupnost služby Azure AD SSPR

Samoobslužné resetování HESLA Azure AD je k dispozici ve třech úrovních v závislosti na vaše předplatné:

* **Azure AD Free**: výhradně Cloudový správci můžou resetovat vlastní hesla.
* **Azure AD Basic** ani na žádného **placené předplatné služeb Office 365**: uživatelů pouze cloudu můžou resetovat svoje vlastní hesla.
* **Azure AD Premium**: každý uživatel nebo správce, včetně cloudového federované, předávací ověřování, nebo uživatelům synchronizaci hodnot hash hesel, můžete resetovat hesla. Místních hesel vyžadují zpětný zápis hesla, aby byla povolená.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD ceny, smlouvy SLA, aktualizace a plány do budoucna

Další informace o licencích, ceny a plánech do budoucna najdete na následujících webech:

* [Podrobnosti o cenách služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Ceny Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/)
* [Smlouva o úrovni služeb pro Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Aktualizace Azure](https://azure.microsoft.com/updates/)
* [Plány Azure do budoucna](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Další postup

* Jste připravení začít s samoobslužné resetování HESLA? [Nastavení hesla pomocí samoobslužné služby Azure AD resetování](quickstart-sspr.md).
* Naplánovat úspěšné nasazení samoobslužného resetování HESLA svým uživatelům pomocí pokynů v naší [zavedení průvodce](howto-sspr-deployment.md).
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
