---
title: Nasadit nástroj pro správu – Azure
description: Jak nainstalovat nástroj uživatelského rozhraní pro správu prostředků ve verzi preview virtuální plochy Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 9579db9836ef41706f2c6be09570fa7c1459e14f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620460"
---
# <a name="tutorial-deploy-a-management-tool"></a>Kurz: Nasazení nástroje pro správu

Nástroj pro správu poskytuje uživatelské rozhraní (UI) pro správu prostředků služby Microsoft Virtual Desktop Preview. V tomto kurzu se dozvíte, jak nasadit a připojit nástroj pro správu.

>[!NOTE]
>Tyto pokyny se týkají konfigurace specifické pro virtuální plochy Windows ve verzi Preview, který lze použít s existujícími procesy vaší organizace.

## <a name="important-considerations"></a>Důležité informace

Protože aplikace vyžaduje souhlas používání virtuální plochy Windows, tento nástroj nepodporuje scénáře Business-to-Business (B2B). Každý tenant Azure Active Directory (AAD) předplatné bude nutné vlastní samostatného nasazení nástroje pro správu.

Tento nástroj pro správu je ukázka. Microsoft bude poskytovat důležité zabezpečení a aktualizace kvality. [Zdrojový kód je k dispozici na Githubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Zákazníci a partneři můžou přizpůsobit nástroj podle svých obchodních potřeb.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co je potřeba spustit šablonu Azure Resource Manageru

Před nasazením šablony Azure Resource Manageru, musíte uživatele služby Azure Active Directory k nasazení uživatelské rozhraní pro správu. Tento uživatel musí:

- Zakázali Azure Multi-Factor Authentication (MFA)
- Nemáte oprávnění k vytváření prostředků v předplatném Azure
- Oprávnění k vytvoření aplikace Azure AD. Postupujte podle těchto kroků zkontrolujte, jestli má uživatel [požadovaná oprávnění](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Po nasazení šablony Azure Resource Manageru, budete chtít spustit uživatelské rozhraní pro ověření pro správu. Tento uživatel musí:
- Máte přiřazení role k zobrazení nebo úpravám tenanta virtuálního klienta Windows

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Spustit šablonu Azure Resource Manageru pro zřízení uživatelské rozhraní pro správu

Než začnete, ujistěte se aplikace server i klient mají souhlasu návštěvou [Windows virtuální plochy souhlas stránky](https://rdweb.wvd.microsoft.com) pro Azure Active Directory (AAD) reprezentovat.

Postupujte podle těchto pokynů k nasazení šablony Azure Resource Manageru:

1. Přejděte [stránku GitHub Azure vzdálené plochy – šablony](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Nasazení šablony do Azure.
    - Pokud nasazujete v rámci předplatného Enterprise, posuňte se dolů a vyberte **nasadit do Azure**. Zobrazit [pokyny pro parametry šablony](#guidance-for-template-parameters).
    - Pokud nasazujete v rámci předplatného poskytovatele Cloud Solution Provider, postupujte podle těchto pokynů můžete nasadit do Azure:
        1. Posuňte se dolů a klikněte pravým tlačítkem na **nasadit do Azure**a pak vyberte **umístění propojení kopírování**.
        2. Otevřete textový editor, například Poznámkový blok a vložte odkaz existuje.
        3. Hned po <https://portal.azure.com/> a před hashtagem, publikovat ho (#), zadejte zavináč (@) následovaný název domény tenantu. Tady je příklad formátu: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Přihlaste se k webu Azure portal jako uživatel s oprávněními správce nebo přispěvatele pro předplatné poskytovatele Cloud Solution Provider.
        5. Vložte odkaz, který jste zkopírovali do textového editoru, a do adresního řádku.

### <a name="guidance-for-template-parameters"></a>Pokyny pro parametry šablony
Tady je postup pro konfiguraci nástroje zadat parametry:

- Toto je adresa URL zprostředkovatele vzdálené plochy: https:\//rdbroker.wvd.microsoft.com/
- Toto je adresa URL prostředku: https:\//mrs-prod.ame.gbl/mrs-RDInfra-prod
- Použijte svoje přihlašovací údaje AAD se používá služba MFA zakázané, aby se přihlaste k Azure. Zobrazit [co je potřeba spustit šablonu Azure Resource Manageru](#what-you-need-to-run-the-azure-resource-manager-template).
- Použijte jedinečný název aplikace, který se zaregistruje ve službě Azure Active Directory pro nástroj pro správu; například Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Zadejte svůj souhlas pro nástroj pro správu

Po GitHub Azure Resource Manageru šablony se dokončí, budete najít skupinu prostředků obsahující dvě aplikace služeb společně s jeden plán služby app service na webu Azure Portal.

Bylo přihlásit a používat nástroj pro správu, bude nutné poskytnout souhlas pro novou aplikaci Azure Active Directory, která souvisí s nástroj pro správu. Tím, že poskytuje souhlasu, chcete povolit nástroj pro správu pro volání virtuální plochy Windows management jménem uživatele, který je podepsaný do nástroje.

![Snímek obrazovky znázorňující zadání při vyjadřujete souhlas s uživatelského rozhraní nástroje pro správu oprávnění.](media/management-ui-delegated-permissions.png)

Chcete-li zjistit, který uživatel můžete použít k přihlášení k nástroji, přejděte na vaše [stránka nastavení uživatele Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) a poznamenejte si hodnoty pro **uživatelé můžou udělit souhlas s aplikací, které přistupují k firemním datům jejich jménem** .

![Snímek obrazovky zobrazující, pokud se uživatelé můžou udělit souhlas až po aplikace právě své uživatele.](media/management-ui-user-consent-allowed.png)

- Pokud je hodnota nastavena na **Ano**, můžete přihlásit pomocí libovolného uživatelského účtu ve službě Azure Active Directory a poskytnout souhlas pouze pro tohoto uživatele. Nicméně pokud se nástroji pro správu jako jiný uživatel později, musíte provést stejné souhlasu znovu.
- Pokud je hodnota nastavena na **ne**, musíte přihlásit jako globální správce ve službě Azure Active Directory a poskytnout souhlas správce pro všechny uživatele v adresáři. Žádní uživatelé se potýkají výzva k povolení spuštění.


Jakmile se rozhodnete, který uživatel bude používat k poskytování souhlas, postupujte podle těchto pokynů k poskytování vyjadřujete souhlas s nástroji:

1. Přejděte na prostředky Azure, vyberte prostředek Azure App Service s názvem, který jste zadali v šabloně (například Apr3UX) a přejděte na adresu URL přidruženou. například <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Přihlaste se pomocí odpovídajícího účtu uživatele Azure Active Directory.
3. Pokud jste ověření s globálním správcem, teď můžete vybrat zaškrtávací políčko k **souhlas jménem svojí organizace**. Vyberte **přijmout** poskytnout souhlas.
   
   ![Snímek obrazovky zobrazující stránku úplné souhlas, že uživatel nebo správce se zobrazí.](media/management-ui-consent-page.png)

To teď přejdete na nástroje pro správu.

## <a name="use-the-management-tool"></a>Použijte nástroj pro správu

Nástroj pro správu po zadání souhlasu pro organizaci nebo pro zadaného uživatele, dostanete v okamžiku.

Postupujte podle těchto pokynů a spustí se nástroj pro:

1. Vyberte prostředek Azure App Service s názvem, který jste zadali v šabloně (například Apr3UX) a přejděte na adresu URL přidruženou. například <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Přihlaste se pomocí svých přihlašovacích údajů Windows virtuálního klienta.
3. Po zobrazení výzvy k výběru skupiny Tenanta, vyberte **výchozí skupiny Tenanta** z rozevíracího seznamu.

> [!NOTE]
> Pokud máte skupinu vlastního Tenanta, zadejte název ručně namísto výběrem z rozevíracího seznamu.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak nasadit a připojit se k nástroj pro správu, se dozvíte, jak pomocí služby Azure Service Health můžete sledovat potíže se službami a poradci pro stav.

> [!div class="nextstepaction"]
> [Nastavte si výstrahy kurz service](./set-up-service-alerts.md)
