---
title: Příprava Azure Migrate pro práci s nástroji/stěhovací společnosti ISV
description: Příprava Azure Migrate pro práci s nástroji/stěhovací společnosti ISV
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906979"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>Příprava na práci s nástroji/stěhovací společnosti ISV

Pokud jste přidali [Nástroj ISV](migrate-services-overview.md#isv-integration)nebo stěhovací společnost do projektu Azure Migrate, je nutné před propojením nástroje připravit několik kroků a odesílat data do Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Ověřit oprávnění služby Azure AD

Váš uživatelský účet Azure potřebuje několik oprávnění:

- Oprávnění k registraci aplikace Azure AD s vaším klientem Azure.
- Oprávnění přidělit roli aplikaci Azure AD na úrovni předplatného.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Nastavení oprávnění k registraci aplikace Azure AD

1. V Azure Active Directory ověřte roli svého účtu. Máte-li roli uživatele, klikněte na tlačítko **nastavení uživatele** vlevo a ověřte, zda mohou uživatelé registrovat aplikace.
2. Pokud je tato možnost nastavená na **hodnotu Ano**, můžou aplikace zaregistrovat všechny uživatele v TENANTOVI Azure AD. Pokud ne, můžou aplikace registrovat jenom uživatelé s oprávněními správce.
3. Pokud nemáte oprávnění, uživatel s právy pro správu může poskytnout svůj uživatelský účet s rolí [správce aplikace](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) , aby bylo možné aplikaci zaregistrovat.
4. Po propojení nástroje s Azure Migrate může správce odebrat roli z vašeho účtu.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Nastavení oprávnění k přiřazení role k aplikaci Azure AD
 
Ve vašem předplatném Azure vyžaduje váš účet **Microsoft. Authorization/*/Write Access**, aby bylo možné přiřadit roli k aplikaci Azure AD. 

1. Pokud chcete kontrolovat oprávnění předplatného, v Azure Portal otevřete **předplatná**.
2. Vyberte příslušné předplatné. Pokud ho nevidíte, vyberte **Filtr globálních předplatných**. 
3. Vyberte **Moje oprávnění**. Pak vyberte **kliknutím sem zobrazíte informace o úplných přístupech k tomuto předplatnému**.
4. V > **zobrazení** **přiřazení rolí**a zkontrolujte oprávnění.
5. Pokud váš účet nemá oprávnění, požádejte správce předplatného, aby vás přidal do role [Správce přístupu uživatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) nebo do role [vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) .
 

## <a name="start-using-the-tool"></a>Začněte používat nástroj.

1. Pokud ještě nemáte licenci nebo bezplatnou zkušební verzi nástroje, klikněte v položce nástroje v Azure Migrate v části **Registrovat**na další **informace**.
2. V nástroji postupujte podle pokynů, propojte nástroj s Azure Migrate projektem a odešlete data do Azure Migrate.

## <a name="next-steps"></a>Další kroky

K odeslání dat do Azure Migrate postupujte podle pokynů ISV nebo stěhovací společnosti.

   
