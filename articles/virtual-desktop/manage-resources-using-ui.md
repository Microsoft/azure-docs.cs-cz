---
title: Nasazení nástroje pro správu pomocí šablony Azure Resource Manager – Azure
description: Jak nainstalovat nástroj uživatelského rozhraní se šablonou Azure Resource Manager pro správu prostředků virtuálních klientů Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
ms.openlocfilehash: 032062dd200781b6d1f5abeb2391ae75c4c43e6a
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367306"
---
# <a name="deploy-a-management-tool-with-an-azure-resource-manager-template"></a>Nasazení nástroje pro správu pomocí šablony Azure Resource Manager

Pokyny v tomto článku vám sdělí, jak nasadit uživatelské rozhraní pomocí šablony Azure Resource Manager.

## <a name="important-considerations"></a>Důležité informace

Vzhledem k tomu, že aplikace vyžaduje souhlas s používáním virtuálního klienta Windows, tento nástroj nepodporuje scénáře B2B (Business-to-Business). Každé předplatné tenanta Azure Active Directory (AAD) bude potřebovat vlastní samostatné nasazení nástroje pro správu.

Tento nástroj pro správu je ukázka. Microsoft bude poskytovat důležité aktualizace zabezpečení a kvality. [Zdrojový kód je k dispozici na GitHubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Zákazníkům a partnerům doporučujeme přizpůsobit nástroj tak, aby vyhovoval jejich obchodním potřebám.

Pro následující prohlížeče jsou kompatibilní s nástrojem pro správu:
- Google Chrome 68 nebo novější
- Microsoft Edge 40,15063 nebo novější
- Mozilla Firefox 52,0 nebo novější
- Safari 10 nebo novější (jenom macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Co potřebujete k nasazení nástroje pro správu

Před nasazením nástroje pro správu budete potřebovat uživatele Azure Active Directory (Azure AD) k vytvoření registrace aplikace a nasazení uživatelského rozhraní pro správu. Tento uživatel musí:

- Máte zakázanou službu Azure Multi-Factor Authentication (MFA)
- Mít oprávnění k vytváření prostředků ve vašem předplatném Azure
- Mít oprávnění k vytvoření aplikace služby Azure AD. Pomocí těchto kroků zkontrolujete, jestli má uživatel požadovaná oprávnění, podle pokynů v tématu [požadovaná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Po nasazení a konfiguraci nástroje pro správu doporučujeme, abyste požádali uživatele, aby spustil uživatelské rozhraní pro správu, aby se zajistilo, že všechno funguje. Uživatel, který spouští uživatelské rozhraní pro správu, musí mít přiřazení role, které jim umožní zobrazit nebo upravit klienta virtuální plochy Windows.

## <a name="deploy-the-management-tool"></a>Nasazení nástroje pro správu

Než začnete, ujistěte se, že serverové a klientské aplikace mají souhlas, a to návštěvou na [stránce pro vyjádření souhlasu s virtuálním počítačem s Windows](https://rdweb.wvd.microsoft.com) pro Azure Active Directory (AAD) reprezentované.

Pomocí těchto pokynů nasaďte šablonu Azure Resource Management:

1. Přejít na [stránku GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Nasaďte šablonu do Azure.
    - Pokud nasazujete v podnikovém předplatném, přejděte dolů a vyberte **nasadit do Azure**. 
    - Pokud nasazujete v rámci předplatného poskytovatele Cloud Solution Provider, při nasazení do Azure postupujte podle těchto pokynů:
        1. Posuňte se dolů a klikněte pravým tlačítkem myši na **nasadit do Azure**a pak vyberte **Kopírovat umístění odkazu**.
        2. Otevřete textový editor, jako je Poznámkový blok, a vložte odkaz sem.
        3. Hned po <https://portal.azure.com/> a před hashtagem (#) zadejte znak hvězdička (@) následovaný názvem domény klienta. Tady je příklad formátu: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Přihlaste se k Azure Portal jako uživatel s oprávněním správce/Přispěvatel k předplatnému poskytovatele Cloud Solution Provider.
        5. Vložte odkaz, který jste zkopírovali do textového editoru, do adresního řádku.
3. Při zadávání parametrů udělejte toto:
    - Pro parametr **isServicePrincipal** vyberte false ( **NEPRAVDA**).
    - Pro přihlašovací údaje zadejte svoje přihlašovací údaje služby Azure AD se zakázaným ověřováním službou Multi-Factor Authentication. Tyto přihlašovací údaje budou použity k vytvoření aplikace služby Azure AD a prostředků Azure. Další informace najdete v tématu [co potřebujete k nasazení nástroje pro správu](#what-you-need-to-deploy-the-management-tool).
    - Pro **ApplicationName**použijte jedinečný název aplikace, který se zaregistruje ve vašem Azure Active Directory. Tento název se použije taky pro adresu URL webové aplikace. Můžete například použít název, například "Apr3UX".
4. Jakmile zadáte parametry, přijměte podmínky a zvolte **koupit**.

## <a name="provide-consent-for-the-management-tool"></a>Poskytnutí souhlasu pro nástroj pro správu

Po dokončení šablony Azure Resource Manager GitHubu najdete v Azure Portal skupinu prostředků, která obsahuje dvě aplikační služby společně s jedním plánem služby App Service.

Před přihlášením a použitím nástroje pro správu musíte poskytnout souhlas nové aplikace Azure AD přidružené k nástroji pro správu. Poskytnutí souhlasu umožňuje nástroji pro správu provést volání správy virtuálních počítačů s Windows jménem uživatele, který je aktuálně přihlášený k nástroji.

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
4. Když vyberete **výchozí skupinu tenantů**, měla by se zobrazit nabídka na levé straně okna. V této nabídce najděte název skupiny tenantů a vyberte ji.
  
  > [!NOTE]
  > Pokud máte vlastní skupinu tenantů, zadejte název ručně místo volby v rozevíracím seznamu.

## <a name="report-issues"></a>Nahlásit problémy

Pokud jste nahlásili mezi problémy s nástrojem pro správu nebo jinými nástroji pro virtuální počítače s Windows, postupujte podle pokynů v části [šablony Azure Resource Manager pro vzdálenou plochu](https://github.com/Azure/RDS-Templates/blob/master/README.md) , které jim podávají oznámení na GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit a připojit se k nástroji pro správu, se dozvíte, jak pomocí služby Azure Services monitorovat problémy se službou a Poradce pro stav. Další informace najdete v našem [kurzu nastavení výstrah služby](./set-up-service-alerts.md).