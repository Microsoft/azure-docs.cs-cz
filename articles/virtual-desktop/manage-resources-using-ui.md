---
title: Nasazení nástroje pro správu pomocí šablony Azure Resource Manager – Azure
description: Jak nainstalovat nástroj uživatelského rozhraní pomocí šablony Azure Resource Manager pro správu prostředků Virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dac17da4aa3d3d73ccbbc101cba4326a17fec9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127784"
---
# <a name="deploy-a-management-tool-with-an-azure-resource-manager-template"></a>Nasazení nástroje pro správu pomocí šablony Azure Resource Manageru

Pokyny v tomto článku vám řeknou, jak nasadit uživatelské tlačítko pomocí šablony Azure Resource Manager.

## <a name="important-considerations"></a>Důležité informace

Vzhledem k tomu, že aplikace vyžaduje souhlas s interakcí s Windows Virtual Desktop, tento nástroj nepodporuje scénáře Business-to-Business (B2B). Každé předplatné klienta Služby Azure Active Directory (AAD) bude potřebovat vlastní samostatné nasazení nástroje pro správu.

Tento nástroj pro správu je ukázka. Společnost Microsoft bude poskytovat důležité aktualizace zabezpečení a kvality. [Zdrojový kód je k dispozici v GitHubu](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Zákazníkům a partnerům se doporučuje, aby nástroj přizpůsobili tak, aby vyhovoval jejich obchodním potřebám.

Následující prohlížeče jsou kompatibilní s nástrojem pro správu:
- Google Chrome 68 nebo novější
- Microsoft Edge 40.15063 nebo novější
- Mozilla Firefox 52.0 nebo novější
- Safari 10 nebo novější (pouze macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Co potřebujete k nasazení nástroje pro správu

Před nasazením nástroje pro správu budete potřebovat uživatele služby Azure Active Directory (Azure AD), který vytvoří registraci aplikace a nasadí uživatelské rozhraní pro správu. Tento uživatel musí:

- Zakázáno je azure multifaktorové ověřování (MFA)
- Máte oprávnění k vytváření prostředků ve vašem předplatném Azure
- Máte oprávnění k vytvoření aplikace Azure AD. Podle těchto kroků zkontrolujte, zda má uživatel požadovaná oprávnění podle pokynů v [části Požadovaná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Po nasazení a konfiguraci nástroje pro správu doporučujeme požádat uživatele o spuštění uživatelského rozhraní pro správu, abyste se ujistili, že vše funguje. Uživatel, který spustí uživatelské rozhraní pro správu, musí mít přiřazení role, které mu umožní zobrazit nebo upravit klienta virtuální plochy Windows.

## <a name="deploy-the-management-tool"></a>Nasazení nástroje pro správu

Než začnete, ujistěte se, že server ové a klientské aplikace mají souhlas tím, že navštívíte [stránku souhlasu virtuální plochy Windows](https://rdweb.wvd.microsoft.com) pro reprezentovanou službu Azure Active Directory (AAD).

Podle těchto pokynů nasaďte šablonu Azure Resource Management:

1. Přejděte na [stránku GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Nasaďte šablonu do Azure.
    - Pokud nasazujete v předplatném Enterprise, posuňte se dolů a vyberte **Nasazení do Azure**. 
    - Pokud nasazujete v předplatném poskytovatele cloudových řešení, nasadit se do Azure podle následujících pokynů:
        1. Posuňte se dolů a klikněte pravým tlačítkem myši **na Nasazení do Azure**a vyberte kopírovat umístění **odkazu**.
        2. Otevřete textový editor, jako je Poznámkový blok, a vložte odkaz tam.
        3. Hned <https://portal.azure.com/> za a před hashtagem (#) zadejte znak @následovaný názvem domény klienta. Zde je příklad formátu: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Přihlaste se k portálu Azure jako uživatel s oprávněními správce/přispěvatele k předplatnému poskytovatele cloudových řešení.
        5. Vložte odkaz, který jste zkopírovali do textového editoru, do adresního řádku.
3. Při zadávání parametrů postupujte takto:
    - Pro parametr **isServicePrincipal** vyberte **hodnotu false**.
    - Pro přihlašovací údaje zadejte přihlašovací údaje Azure AD s vícefaktorovým ověřováním zakázáno. Tato pověření se použijí k vytvoření aplikace Azure AD a prostředků Azure. Další informace najdete v tématu [Co potřebujete k nasazení nástroje pro správu](#what-you-need-to-deploy-the-management-tool).
    - Pro **applicationName**použijte jedinečný název aplikace, která bude registrovaná ve vaší službě Azure Active Directory. Tento název bude použit také pro adresu URL webové aplikace. Můžete například použít název jako "Apr3UX".
4. Jakmile zadáte parametry, přijměte podmínky a vyberte **možnost Koupit**.

## <a name="provide-consent-for-the-management-tool"></a>Poskytnout souhlas s nástrojem pro správu

Po dokončení šablony GitHub Azure Resource Manager najdete skupinu prostředků obsahující dvě aplikační služby spolu s jedním plánem služby aplikace na webu Azure Portal.

Než se přihlásíte a použijete nástroj pro správu, musíte poskytnout souhlas pro novou aplikaci Azure AD přidruženou k nástroji pro správu. Poskytnutí souhlasu umožňuje nástroji pro správu, aby služba Správa virtuální plochy systému Windows volala jménem uživatele, který je k němu aktuálně přihlášen.

![Snímek obrazovky s oprávněními poskytovanými při souhlasu s nástrojem pro správu uj.](media/management-ui-delegated-permissions.png)

Chcete-li zjistit, který uživatel se můžete k nástroji přihlásit, přejděte na [stránku nastavení uživatele služby Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) a poznamenejte si hodnotu, kterou **mohou uživatelé udělit souhlas s tím, aby aplikace přistupující k firemním datům jejich jménem**.

![Snímek obrazovky s tím, zda mohou uživatelé udělit souhlas s aplikacemi pouze pro svého uživatele.](media/management-ui-user-consent-allowed.png)

- Pokud je hodnota nastavena na **Ano**, můžete se přihlásit pomocí libovolného uživatelského účtu ve službě Azure Active Directory a poskytnout souhlas jenom pro tohoto uživatele. Pokud se však později přihlásíte k nástroji pro správu s jiným uživatelem, musíte znovu provést stejný souhlas.
- Pokud je hodnota nastavená na **ne**, musíte se přihlásit jako globální správce ve službě Azure Active Directory a poskytnout souhlas správce všem uživatelům v adresáři. Žádní další uživatelé nebudou čelit výzvě k souhlasu.


Jakmile se rozhodnete, kterého uživatele budete používat k udělení souhlasu, postupujte podle těchto pokynů a k poskytnutí souhlasu s nástrojem:

1. Přejděte na prostředky Azure, vyberte prostředek Služby Azure App Services s názvem, který jste zadali v šabloně (například Apr3UX) a přejděte na adresu URL, která je k ní přidružena. například <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Přihlaste se pomocí příslušného uživatelského účtu služby Azure Active Directory.
3. Pokud jste se ověřili u globálního správce, můžete nyní zaškrtnout políčko **Souhlas jménem vaší organizace**. Chcete-li poskytnout souhlas, vyberte **možnost Přijmout.**
   
   ![Snímek obrazovky s úplným souhlasem, který uvidí uživatel nebo správce.](media/management-ui-consent-page.png)

Tím se nyní dostanete k nástroji pro správu.

## <a name="use-the-management-tool"></a>Použití nástroje pro správu

Po udělení souhlasu pro organizaci nebo pro určeného uživatele máte kdykoli přístup k nástroji pro správu.

Chcete-li nástroj spustit, postupujte podle následujících pokynů:

1. Vyberte prostředek Služby Azure App Services s názvem, který jste zadali v šabloně (například Apr3UX) a přejděte na adresu URL, která je k ní přidružena. například <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Přihlaste se pomocí přihlašovacích údajů k virtuální ploše Windows.
3. Po zobrazení výzvy k výběru skupiny klientů vyberte v rozevíracím seznamu **výchozí skupinu klientů.**
4. Vyberete-li **výchozí skupinu klientů**, měla by se na levé straně okna zobrazit nabídka. V této nabídce najděte název skupiny klientů a vyberte ji.
  
  > [!NOTE]
  > Pokud máte vlastní skupinu klientů, zadejte název ručně namísto výběru z rozevíracího seznamu.

## <a name="report-issues"></a>Nahlášení potíží

Pokud narazíte na nějaké problémy s nástrojem pro správu nebo jinými nástroji virtuální plochy Windows, postupujte podle pokynů v [šablonách Azure Resource Manager pro vzdálenou plochu a](https://github.com/Azure/RDS-Templates/blob/master/README.md) oznamte je na GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit a připojit se k nástroji pro správu, můžete se dozvědět, jak používat Azure Service nápovědu ke sledování problémů se službami a zdravotní ch odvažování. Další informace najdete v našem [kurzu Nastavení upozornění služby](./set-up-service-alerts.md).