---
title: Podrobná Konfigurace hostovaného testovacího disku v Azure Marketplace
description: Vysvětlení kroků konfigurace hostovaného testovacího disku na komerčním webu Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 11/06/2020
ms.openlocfilehash: 8bc83958e4aaea5f84654a404e4afbfd60b19b0d
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414640"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Podrobná konfigurace pro hostované testovací jednotky

Tento článek popisuje, jak nakonfigurovat hostovaný testovací disk pro Dynamics 365 pro zákaznickou zapojení nebo Dynamics 365 pro operace.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Konfigurace pro Dynamics 365 Customer Engagement

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/).
2. Pokud nemůžete získat přístup k výše uvedenému odkazu, je třeba [Odeslat žádost o](https://appsource.microsoft.com/partners/list-an-app) publikování aplikace. Po kontrole žádosti vám bude uděleno oprávnění k zahájení procesu publikování.
3. Najděte si existující **dynamics 365 pro nabídku Customer Engagement** nebo vytvořte novou nabídku **Dynamics 365 for Customer Engagement** .
4. Zaškrtněte políčko **Povolit testovací jednotku** a vyberte **typ testovací jednotky** (viz odrážka níže) a pak vyberte **Uložit**.

    [![Zaškrtnutí políčka Povolit testovací jednotku.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Typ testovací jednotky** – vyberte **Microsoft Hosted (Dynamics 365 for customer Engagement & PowerApps)**. To znamená, že Microsoft bude hostovat a udržovat službu, která provede test zřizování a zrušení zřizování uživatelů.

5. Pomocí [těchto pokynů](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)udělte Microsoft AppSource oprávnění ke zřízení a zrušení zřízení uživatelů testovacích jednotek ve vašem tenantovi. V tomto kroku vygenerujete **aplikace Azure AD ID** a aplikace Azure AD hodnoty **klíče** uvedené níže.
6. Vyplňte tato pole na stránce **Technická konfigurace testovacích jednotek** .

    [![Stránka technické konfigurace testovacích jednotek.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximální počet souběžných testovacích jednotek** – počet souběžných uživatelů, kteří můžou mít spuštěnou aktivní testovací jednotku ve stejnou dobu. Každý uživatel bude používat licenci Dynamics v době, kdy je jejich testovací jednotka aktivní, takže musíte mít k dispozici alespoň tento počet licencí Dynamics pro uživatele testovacích jednotek. Doporučili jsme 3 až 5.
    - **Doba trvání testovacích jednotek** – počet hodin, po které bude testovací jednotka uživatele aktivní. Po vypršení časového limitu bude uživatel ve vašem tenantovi zrušeno. V závislosti na složitosti vaší aplikace doporučujeme 2-24 hodin. Uživatel si může vždycky vyžádat jinou testovací jednotku, pokud k nim dojde nevčas a chcete znovu získat přístup k testovací jednotce.
    - **Adresa URL instance** – adresa URL, na kterou se uživatel testovací jednotky pošle při spuštění testovací jednotky. Obvykle se jedná o adresu URL vaší instance Dynamics 365, na které je nainstalována vaše aplikace a ukázková data. Příklad hodnoty: `https://testdrive.crm.dynamics.com` .
    - **Instance URL webového rozhraní API** – adresa URL webového rozhraní API vaší Instance Dynamics 365 Načtěte tuto hodnotu tak, že se přihlásíte ke své instanci Microsoft Dynamics 365 a přejdete na **Nastavení**  >  **přizpůsobení**  >  **Developer Resources**  >  **instance zdrojů webové rozhraní API** a zkopírujete adresu (URL). Ukázková hodnota:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Příklad instance webového rozhraní API":::

    - **Název role** – název vlastní role zabezpečení Dynamics 365, kterou jste vytvořili pro Test Drive, nebo můžete použít stávající roli. Nová role by měla mít do role přidat minimální požadovaná oprávnění, která by se mohla přihlašovat do instance Engagement zákazníka. Projděte si [minimální oprávnění požadovaná pro přihlášení k Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Toto je role, která se uživatelům přiřadí během své testovací jednotky. Příklad hodnoty: `testdriverole` .
    
        > [!IMPORTANT]
        > Zajistěte, aby se kontrola skupiny zabezpečení nepřidala. To umožňuje, aby byl uživatel synchronizovaný s instancí Engagement zákazníka.

    - **ID tenanta Azure Active Directory** – ID tenanta Azure pro vaši instanci Dynamics 365. Tuto hodnotu načtete tak, že se přihlásíte Azure Portal a přejdete do **Azure Active Directory**  >  **vlastností** a zkopírujete ID adresáře. Příklad hodnoty: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory název tenanta** – název tenanta Azure pro vaši instanci Dynamics 365. Použijte formát `<tenantname>.onmicrosoft.com`. Příklad hodnoty: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory ID aplikace** – id aplikace Azure Active Directory (AD), kterou jste vytvořili v kroku 5. Příklad hodnoty: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory klientský tajný klíč aplikace** – tajný klíč pro aplikaci Azure AD vytvořenou v kroku 5. Příklad hodnoty: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Zadejte podrobnosti o seznamu Marketplace. Vyberte **jazyk** pro zobrazení dalších požadovaných polí.

    [![Stránka s podrobnostmi na seznamu Marketplace](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Popis** – přehled testovacích jednotek. Tento text se zobrazí uživateli při zřizování testovací jednotky. Toto pole podporuje kód HTML, pokud chcete zadat formátovaný obsah.
    - **User Manual** – uživatelská příručka PDF, která pomáhá testovat, jak používat vaši aplikaci.
    - **Ukázkové video ke zkušební jednotce** – video, které prezentuje vaši aplikaci (volitelné).

## <a name="configure-for-dynamics-365-operations"></a>Konfigurace pro operace Dynamics 365

2. Pokud nemůžete získat přístup k výše uvedenému odkazu, je třeba [Odeslat žádost o](https://appsource.microsoft.com/partners/list-an-app) publikování aplikace. Po kontrole žádosti vám bude uděleno oprávnění k zahájení procesu publikování.
3. Vyhledejte existující nabídku **dynamics 365 for Operations** nebo vytvořte novou nabídku **Dynamics 365 for Operations** .
4. Zaškrtněte políčko **Povolit testovací jednotku** a vyberte **typ testovací jednotky** (viz odrážka níže) a pak vyberte **Uložit**.

    [![Zaškrtněte políčko Povolit testovací jednotku.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Typ testovací jednotky** – vyberte možnost **Dynamics 365 for Operations** . To znamená, že Microsoft bude hostovat a udržovat službu, která provede test zřizování a zrušení zřizování uživatelů.

5. Pomocí [těchto pokynů](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)udělte Microsoft AppSource oprávnění ke zřízení a zrušení zřízení uživatelů testovacích jednotek ve vašem tenantovi. V tomto kroku vygenerujete **aplikace Azure AD ID** a aplikace Azure AD hodnoty **klíče** uvedené níže.
6. Vyplňte tato pole na stránce **Technická konfigurace testovacích jednotek** .

    [![Stránka technické konfigurace Marketplace](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximální počet souběžných testovacích jednotek** – počet souběžných uživatelů, kteří můžou mít spuštěnou aktivní testovací jednotku ve stejnou dobu. Každý uživatel bude používat licenci Dynamics v době, kdy je jejich testovací jednotka aktivní, takže musíte mít k dispozici alespoň tento počet licencí Dynamics pro uživatele testovacích jednotek. Doporučili jsme 3 až 5.
    - **Doba trvání testovacích jednotek** – počet hodin, po které bude testovací jednotka uživatele aktivní. Po vypršení časového limitu bude uživatel ve vašem tenantovi zrušeno. V závislosti na složitosti vaší aplikace doporučujeme 2-24 hodin. Uživatel si může vždycky vyžádat jinou testovací jednotku, pokud k nim dojde nevčas a chcete znovu získat přístup k testovací jednotce.
    - **Adresa URL instance** – adresa URL, na kterou se uživatel testovací jednotky pošle při spuštění testovací jednotky. Obvykle se jedná o adresu URL vaší instance Dynamics 365, na které je nainstalována vaše aplikace a ukázková data. Příklad hodnoty: `https://testdrive.crm.dynamics.com` .
    - **ID tenanta Azure Active Directory** – ID tenanta Azure pro vaši instanci Dynamics 365. Tuto hodnotu načtete tak, že se přihlásíte Azure Portal a přejdete do **Azure Active Directory**  >  **vlastností** a zkopírujete ID adresáře. Příklad hodnoty: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory název tenanta** – název tenanta Azure pro vaši instanci Dynamics 365. Použijte formát `<tenantname>.onmicrosoft.com`. Příklad hodnoty: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory ID aplikace** – id aplikace Azure Active Directory (AD), kterou jste vytvořili v kroku 5. Příklad hodnoty: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory klientský tajný klíč aplikace** – tajný klíč pro aplikaci Azure AD vytvořenou v kroku 5. Příklad hodnoty: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Právní subjekt pro zkušební verzi** – poskytněte právní entitu pro přiřazení zkušební verze uživatele. Novou entitu můžete vytvořit vytvořením [nebo úpravou právnické osoby](https://technet.microsoft.com/library/hh242184.aspx).
    - **Název role** – název AOT (strom aplikačních objektů) vlastní role zabezpečení Dynamics 365, kterou jste vytvořili pro testovací verzi. Toto je role, která se uživatelům přiřadí během své testovací jednotky.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Stránka Konfigurace zabezpečení":::

7. Zadejte podrobnosti o seznamu Marketplace. Vyberte **jazyk** pro zobrazení dalších požadovaných polí.

    [![Stránka s podrobnostmi na seznamu Marketplace](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Popis** – přehled testovacích jednotek. Tento text se zobrazí uživateli při zřizování testovací jednotky. Toto pole podporuje kód HTML, pokud chcete zadat formátovaný obsah.
    - **User Manual** – uživatelská příručka PDF, která pomáhá testovat, jak používat vaši aplikaci.
    - **Ukázkové video ke zkušební jednotce** – video, které prezentuje vaši aplikaci (volitelné).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->
