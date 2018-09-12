---
title: Povolení integrace LinkedIn připojení v Azure Active Directory | Dokumentace Microsoftu
description: Vysvětluje, jak povolit nebo zakázat s účtem LinkedIn pro aplikace Microsoftu ve službě Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44392232"
---
# <a name="linkedin-account-connections"></a>Propojení s účtem LinkedIn

V tomto článku se dozvíte, jak povolit nebo zakázat s účtem LinkedIn pro vašeho tenanta v Centru pro správu Azure Active Directory (Azure AD).

> [!IMPORTANT]
> S účtem LinkedIn nastavení aktuálně zavedena s tenanty Azure AD. Když se nasazuje pro vašeho tenanta, je standardně povolená. 
> 
> Výjimky:
> * Toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud pro státní správu USA, Microsoft Cloud Germany nebo Azure a Office 365, kterou provozuje společnost 21Vianet v Číně.
> * Toto nastavení je vypnuto ve výchozím nastavení pro klienty, které jsou zřízené v Německu. Všimněte si, že toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud Germany.
> * Toto nastavení je vypnuto ve výchozím nastavení pro klienty zřízené ve Francii.

> Integrace funguje pouze v případě, že máte povolené *a* Pokud umožníte uživatelům udělit souhlas s aplikacemi, které přistupují k firemním datům jejich jménem. Informace o nastavení souhlasu najdete v tématu [odebrání přístupu uživatele k aplikaci](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Povolení nebo zakázání s účtem LinkedIn pro vašeho tenanta na portálu Azure portal

Můžete povolit nebo zakázat připojení účet LinkedIn pro celého tenanta nebo pouze pro vybrané uživatele ve vašem tenantovi.

1. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu, který má oprávnění globálního správce pro tenanta Azure AD.
2. Vyberte **uživatelé**.
3. Na **uživatelé** okně vyberte **uživatelská nastavení**.
4. V části **s účtem LinkedIn**:
  * Vyberte **Ano** povolit s účtem LinkedIn pro všechny uživatele ve vašem tenantovi
  * Vyberte **vybrané** povolit účet LinkedIn připojení pouze vybrané tenanta uživatele
  * Vyberte **ne** zakázat s účtem LinkedIn pro všechny uživatele ![povolení s účtem LinkedIn](./media/linkedin-integration/linkedin-integration.png)
5. Až to uděláte tak, že vyberete uložit nastavení **Uložit**.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>Povolení nebo zakázání s účtem LinkedIn pro vašeho tenanta pomocí zásad skupiny

1. Stáhněte si [souborů šablon pro správu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahovat **ADMX** soubory a zkopírujte je do centrálního úložiště.
3. Otevřete správu zásad skupiny.
4. Vytvoření objektu zásad skupiny s následujícím nastavením: **konfigurace uživatele** > **šablony pro správu** > **Microsoft Office 2016**  >  **Různé** > **funkcí služby LinkedIn zobrazit v aplikacích Office**.
5. Vyberte **povolené** nebo **zakázané**.
  
 Stav | Efekt
------ | ------
**Povoleno** | **Funkcí služby LinkedIn zobrazit v aplikacích Office** je povolené nastavení v možnostech Office 2016. Uživatelé ve vaší organizaci můžou používat funkce Linkedinu v aplikacích Office.
 **Zakázané** | **Funkcí služby LinkedIn zobrazit v aplikacích Office** zakázáno v možnostech Office 2016 a toto nastavení nejde změnit koncovým uživatelům. Uživatelé ve vaší organizaci nemůžou používat funkce Linkedinu ve svých aplikacích Office 2016.

Tyto zásady skupiny ovlivní pouze aplikace Office 2016 pro místní počítač. Uživatelé mohou vidět funkce Linkedinu v profilové karty v rámci Office 365, i když se zakážou LinkedIn ve svých aplikacích Office 2016.

## <a name="learn-more"></a>Další informace

* [Integrace Linkedinu ve vaší organizaci](linkedin-user-consent.md)

* [LinkedIn informace a funkce v aplikacích Microsoftu](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pro nápovědu LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Další postup
Pomocí následujícího odkazu zobrazíte vaše aktuální s účtem LinkedIn nastavení na portálu Azure portal:

[Konfigurace s účtem LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 