---
title: Nasazení nástroje pro správu – Azure
description: Postup instalace nástroje uživatelského rozhraní pro správu prostředků virtuálních počítačů s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: helohr
ms.openlocfilehash: f07403d8d0b2c6d0dd7a6b851a87b47b0c32501a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679493"
---
# <a name="tutorial-deploy-a-management-tool"></a>Kurz: nasazení nástroje pro správu

Nástroj pro správu poskytuje uživatelské rozhraní (UI) pro správu prostředků virtuálních klientů společnosti Microsoft. V tomto kurzu se naučíte, jak nasadit nástroj pro správu a připojit se k němu.

>[!NOTE]
>Tyto pokyny se týkají konfigurace specifické pro virtuální počítače s Windows, které se dají použít s existujícími procesy vaší organizace.

## <a name="important-considerations"></a>Důležité informace

Vzhledem k tomu, že aplikace vyžaduje souhlas s používáním virtuálního klienta Windows, tento nástroj nepodporuje scénáře B2B (Business-to-Business). Každé předplatné tenanta Azure Active Directory (AAD) bude potřebovat vlastní samostatné nasazení nástroje pro správu.

Tento nástroj pro správu je ukázka. Microsoft bude poskytovat důležité aktualizace zabezpečení a kvality. [Zdrojový kód je k dispozici na GitHubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Zákazníkům a partnerům doporučujeme přizpůsobit nástroj tak, aby vyhovoval jejich obchodním potřebám.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co potřebujete ke spuštění šablony Azure Resource Manager

Před nasazením šablony Azure Resource Manager budete potřebovat Azure Active Directoryho uživatele pro nasazení uživatelského rozhraní pro správu. Tento uživatel musí:

- Máte zakázanou službu Azure Multi-Factor Authentication (MFA)
- Mít oprávnění k vytváření prostředků ve vašem předplatném Azure
- Mít oprávnění k vytvoření aplikace služby Azure AD. Pomocí těchto kroků zjistíte, jestli má uživatel [požadovaná oprávnění](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Po nasazení šablony Azure Resource Manager budete chtít spustit uživatelské rozhraní pro správu, které chcete ověřit. Tento uživatel musí:
- Přiřazení role pro zobrazení nebo úpravy tenanta virtuálních klientů s Windows

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Spuštění šablony Azure Resource Manager pro zřízení uživatelského rozhraní pro správu

Než začnete, ujistěte se, že serverové a klientské aplikace mají souhlas, a to návštěvou na [stránce pro vyjádření souhlasu s virtuálním počítačem s Windows](https://rdweb.wvd.microsoft.com) pro Azure Active Directory (AAD) reprezentované.

Pomocí těchto pokynů nasaďte šablonu Azure Resource Management:

1. Přejít na [stránku GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Nasaďte šablonu do Azure.
    - Pokud nasazujete v podnikovém předplatném, přejděte dolů a vyberte **nasadit do Azure**. Viz [doprovodné materiály k parametrům šablon](#guidance-for-template-parameters).
    - Pokud nasazujete v rámci předplatného poskytovatele Cloud Solution Provider, při nasazení do Azure postupujte podle těchto pokynů:
        1. Posuňte se dolů a klikněte pravým tlačítkem myši na **nasadit do Azure**a pak vyberte **Kopírovat umístění odkazu**.
        2. Otevřete textový editor, jako je Poznámkový blok, a vložte odkaz sem.
        3. Hned po <https://portal.azure.com/> a před hashtagem (#) zadejte znak po (@) následovaný názvem domény klienta. Tady je příklad formátu: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Přihlaste se k Azure Portal jako uživatel s oprávněním správce/Přispěvatel k předplatnému poskytovatele Cloud Solution Provider.
        5. Vložte odkaz, který jste zkopírovali do textového editoru, do adresního řádku.

### <a name="guidance-for-template-parameters"></a>Doprovodné materiály k parametrům šablon
Tady je postup, jak zadat parametry pro konfiguraci nástroje:

- Toto je adresa URL zprostředkovatele VP: https: \//rdbroker. WVD. Microsoft. com/
- Toto je adresa URL prostředku: https: \//MRS-prod. ázev. GBL/paní-RDInfra-prod
- K přihlášení do Azure použijte přihlašovací údaje AAD s povoleným MFA. Podívejte se, [co potřebujete ke spuštění šablony Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Pro nástroj pro správu použijte jedinečný název aplikace, který se zaregistruje ve vašem Azure Active Directory. například Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Poskytnutí souhlasu pro nástroj pro správu

Po dokončení šablony Azure Resource Manager GitHubu najdete v Azure Portal skupinu prostředků, která obsahuje dvě aplikační služby společně s jedním plánem služby App Service.

Předtím, než se přihlásíte a použijete nástroj pro správu, musíte poskytnout souhlas s novou Azure Active Directory aplikací, která je přidružená k nástroji pro správu. Poskytnutím souhlasu umožníte nástroji pro správu provádět volání správy virtuálních počítačů s Windows jménem uživatele, který je přihlášený k nástroji.

![Snímek obrazovky zobrazující oprávnění, která jsou k dispozici při souhlasu nástroje pro správu uživatelského rozhraní.](media/management-ui-delegated-permissions.png)

Chcete-li zjistit, který uživatel, který můžete použít k přihlášení k nástroji, přejít na [stránku nastavení uživatele Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) a poznamenejte si hodnotu pro uživatele, kteří budou mít **souhlas s přístupem k firemním datům jménem společnosti**.

![Snímek obrazovky, který ukazuje, jestli uživatelé můžou udělit souhlas aplikacím jenom pro uživatele.](media/management-ui-user-consent-allowed.png)

- Pokud je hodnota nastavená na **Ano**, můžete se přihlásit pomocí libovolného uživatelského účtu v Azure Active Directory a poskytnout souhlas jenom pro tohoto uživatele. Pokud se ale později přihlásíte k nástroji pro správu pomocí jiného uživatele, musíte stejný souhlas udělat znovu.
- Pokud je hodnota nastavena na **ne**, musíte se přihlásit jako globální správce v Azure Active Directory a poskytnout souhlas správce pro všechny uživatele v adresáři. Žádnému dalšímu uživateli se zobrazí výzva k vyjádření souhlasu.


Jakmile se rozhodnete, který uživatel použijete k poskytnutí souhlasu, postupujte podle těchto pokynů a poskytněte tomuto nástroji souhlas:

1. Přejděte do prostředků Azure, vyberte prostředek Azure App Services s názvem, který jste zadali v šabloně (například Apr3UX), a přejděte na adresu URL, která k němu je přidružená. například <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Přihlaste se pomocí příslušného Azure Active Directory uživatelského účtu.
3. Pokud jste ověřili s globálním správcem, můžete teď zaškrtnout políčko pro **vyjádření souhlasu jménem vaší organizace**. Vyberte **přijmout** pro poskytnutí souhlasu.
   
   ![Snímek obrazovky zobrazující stránku s úplným souhlasem, kterou uvidí uživatel nebo správce.](media/management-ui-consent-page.png)

Nyní přejdete k nástroji pro správu.

## <a name="use-the-management-tool"></a>Použití nástroje pro správu

Po poskytnutí souhlasu organizace nebo zadaného uživatele můžete kdykoli získat přístup k nástroji pro správu.

Při spuštění nástroje postupujte podle těchto pokynů:

1. Vyberte prostředek služby Azure App Services s názvem, který jste zadali v šabloně (například Apr3UX), a přejděte na adresu URL, která je k ní přidružená. například <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Přihlaste se pomocí svých přihlašovacích údajů k virtuálnímu počítači s Windows.
3. Po zobrazení výzvy k výběru skupiny tenantů vyberte v rozevíracím seznamu **výchozí skupinu tenantů** .

> [!NOTE]
> Pokud máte vlastní skupinu tenantů, zadejte název ručně místo volby v rozevíracím seznamu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit nástroj pro správu a připojit se k němu, se dozvíte, jak pomocí Azure Service Health monitorovat problémy se službami a Poradce pro stav.

> [!div class="nextstepaction"]
> [Kurz nastavení výstrah služby](./set-up-service-alerts.md)
