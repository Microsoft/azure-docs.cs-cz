---
title: Příprava Azure Migrate pro práci s nástrojem/stěhovací společnosti ISV
description: Tento článek popisuje, jak připravit Azure Migrate pro práci s nástrojem ISV nebo stěhovací společnosti a jak začít používat nástroj.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 1716db0476169e12822b3f47f7199bf6e2c4ee92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753769"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Příprava na práci s nástrojem ISV nebo jeho stěhovací pracovníky

Pokud jste přidali [Nástroj ISV](migrate-services-overview.md#isv-integration) nebo do projektu Azure Migrate, je nutné provést několik kroků, než propojíte nástroj a odešlete data do Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Ověřit oprávnění služby Azure AD

Váš uživatelský účet Azure potřebuje tato oprávnění:

- Oprávnění k registraci aplikace Azure Active Directory (Azure AD) s vaším klientem Azure
- Oprávnění přidělit roli aplikaci Azure AD na úrovni předplatného


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Nastavení oprávnění k registraci aplikace Azure AD

1. Ve službě Azure AD ověřte roli svého účtu.
2. Pokud máte roli uživatele, vyberte na levé straně **nastavení uživatele** a ověřte, jestli uživatelé můžou registrovat aplikace. Pokud je tato možnost nastavená na **hodnotu Ano**, můžou aplikace zaregistrovat všechny uživatele v TENANTOVI Azure AD. Pokud je nastavená na **ne**, můžou aplikace registrovat jenom správci.   
3. Pokud nemáte oprávnění, uživatel s právy pro správu může poskytnout svůj uživatelský účet s rolí [správce aplikace](../active-directory/roles/permissions-reference.md#application-administrator) , aby bylo možné aplikaci zaregistrovat.
4. Po propojení nástroje s Azure Migrate může správce odebrat roli z vašeho účtu.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Nastavení oprávnění k přiřazení role k aplikaci Azure AD
 
Ve vašem předplatném Azure vyžaduje váš účet **Microsoft. Authorization/*/Write** Access pro přiřazení role k aplikaci Azure AD. 

1. Na webu Azure Portal otevřete **Předplatná**.
2. Vyberte příslušné předplatné. Pokud ho nevidíte, vyberte **Filtr globálních předplatných**. 
3. Vyberte **Moje oprávnění**. Pak vyberte **kliknutím sem zobrazíte informace o úplných přístupech k tomuto předplatnému**.
4. V **zobrazení přiřazení rolí**  >  Zkontrolujte oprávnění. Pokud váš účet nemá oprávnění, požádejte správce předplatného, aby vás přidal do role [Správce přístupu uživatele](../role-based-access-control/built-in-roles.md#user-access-administrator) nebo role [vlastníka](../role-based-access-control/built-in-roles.md#owner) .

## <a name="allow-access-to-urls"></a>Povolení přístupu k adresám URL

V případě nástrojů ISV a Azure Database Pomocník s migrací povolte přístup k adresám URL veřejného cloudu, které jsou shrnuté v tabulce. Pokud pro připojení k Internetu používáte proxy server založený na adrese URL, ujistěte se, že proxy překládá všechny záznamy CNAME přijaté při vyhledávání adres URL. 

**Adresa URL** | **Podrobnosti**
--- | ---
*.portal.azure.com  | Přejděte na Azure Portal. 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *.live.com   | Přihlaste se ke svému předplatnému Azure. 
*.microsoftonline.com<br/> *.microsoftonline-p.com | Vytvořte Azure Active Directory (AD) aplikace pro zařízení, které budou komunikovat s Azure Migrate. 
management.azure.com | Proveďte Azure Resource Manager volání do projektu Azure Migrate.
*.servicebus.windows.net | Komunikace mezi zařízením a EventHub pro posílání zpráv.


## <a name="start-using-the-tool"></a>Začněte používat nástroj.

1. Pokud ještě nemáte licenci nebo bezplatnou zkušební verzi nástroje, v položce nástroje v Azure Migrate v části **Registrovat** vyberte další **informace**.
2. V nástroji postupujte podle pokynů pro odkazování nástroje na projekt Azure Migrate a k odeslání dat do Azure Migrate.

## <a name="next-steps"></a>Další kroky

Pomocí pokynů z ISV nebo stěhovací společnosti odešlete data do Azure Migrate.

   
