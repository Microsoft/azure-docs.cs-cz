---
title: Nasazení Azure Blockchain Workbench
description: Jak nasadit Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/22/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 85a627678f862d783d47013d82bae8b485d7d4e9
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="deploy-azure-blockchain-workbench"></a>Nasazení Azure Blockchain Workbench

Azure Blockchain Workbench se nasazuje pomocí šablony řešení v Azure Marketplace. Šablona zjednodušuje nasazení komponent potřebných k vytvoření blockchain aplikací. Po nasazení Blockchain Workbench poskytuje přístup k aplikacím klienta k vytváření a správě uživatelů a blockchain aplikací.

Další informace o součástech nástroje Blockchain Workbench najdete v tématu [Azure Blockchain Workbench architektura](blockchain-workbench-architecture.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení

Azure Blockchain Workbench potřeba splnit několik předpokladů před nasazení. Požadavky zahrnout registrace konfigurace a aplikace Azure AD.

### <a name="blockchain-workbench-api-app-registration"></a>Registrace aplikací Blockchain Workbench API

Nasazení Blockchain Workbench vyžaduje registraci aplikace Azure AD. Je nutné klienta služby Azure Active Directory (Azure AD) pro registraci aplikace. Můžete použít existujícího klienta nebo vytvořit nového klienta. Pokud používáte existujícího klienta Azure AD, je třeba dostatečná oprávnění k registraci aplikací v rámci klienta Azure AD. Registrace aplikace musí být v klientovi předplatné správce předplatného, které se nasadí Workbench. Další informace o klienty Azure AD najdete v tématu [jak získat klienta služby Active Directory](../active-directory/develop/active-directory-howto-tenant.md) a [integrace aplikací s Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte účet v pravém horním rohu a přepněte do požadovaného Azure AD klienta. Klient musí být správce předplatného klienta předplatného, kde je nasazená Workbench a máte dostatečná oprávnění k registraci aplikací.
3. V levém navigačním podokně, vyberte **Azure Active Directory** služby. Vyberte **registrace aplikace** > **nové registrace aplikace**.

    ![Registrace aplikace](media/blockchain-workbench-deploy/app-registration.png)

4. Zadejte **název** a **přihlašovací adresa URL** pro aplikaci. Vzhledem k tomu, že jsou během nasazení změnit hodnoty, můžete použít zástupný symbol hodnoty. 

    ![Vytvoření aplikace registrace](media/blockchain-workbench-deploy/app-registration-create.png)

    |Nastavení  | Hodnota  |
    |---------|---------|
    |Název | `Blockchain API` |
    |Typ aplikace |Webová aplikace / webové rozhraní API|
    |Přihlašovací adresa URL | `https://blockchainapi` |

5. Vyberte **vytvořit** zaregistrovat aplikaci Azure AD.

### <a name="modify-application-manifest"></a>Upravte manifest aplikace

Dále musíte upravit manifest aplikace pro použití aplikační role v rámci Azure AD k určení Blockchain Workbench správci.  Další informace o manifestů aplikace najdete v tématu [manifest aplikace Azure Active Directory](../active-directory/develop/active-directory-application-manifest.md).

1. Pro aplikaci jste zaregistrovali, vyberte **Manifest** v podokně podrobností zaregistrovanou aplikaci.
2. Generovat identifikátor GUID. Můžete použít příkaz prostředí PowerShell `[guid]::NewGuid()` nebo online nástroje generovat identifikátor GUID. 
3. Chcete-li aktualizovat **appRoles** oddílu manifest. V podokně manifestu upravit, vyberte **upravit** a nahraďte `"appRoles": []` s zadaný formát JSON. Nezapomeňte nahradit hodnotu **id** pole s identifikátorem GUID jste vygenerovali. 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![Upravit manifest](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > Hodnota **správce** je potřeba k identifikaci Blockchain Workbench správci.

4.  Klikněte na tlačítko **Uložit** se uložit změny manifestu aplikace.

### <a name="add-graph-api-required-permissions"></a>Přidání rozhraní Graph API vyžaduje oprávnění

Rozhraní API aplikace musí požádat o oprávnění uživatele pro přístup k adresáři. Nastavte následující požadované oprávnění pro aplikaci API:

1. V registraci aplikace Blockchain API, vyberte **Nastavení > požadovaná oprávnění > Vybrat rozhraní API > Microsoft Graph**.

    ![Výběr rozhraní API](media/blockchain-workbench-deploy/client-app-select-api.png)

    Klikněte na **Vybrat**.

2. V **povolit přístup** pod **oprávnění aplikací**, zvolte **číst úplných profilů všech uživatelů**.

    ![Povolit přístup](media/blockchain-workbench-deploy/client-app-read-perms.png)

    Klikněte na tlačítko **vyberte** klikněte **provádí**.

3. V **požadovaná oprávnění**, vyberte **udělit oprávnění** vyberte **Ano** pro výzvu ověření.

   ![Udělit oprávnění](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   Udělení oprávnění umožňuje Blockchain Workbench pro přístup uživatelé v adresáři. Vyhledávání a přidání členů do Blockchain Workbench je potřeba oprávnění ke čtení.

### <a name="add-graph-api-key-to-application"></a>Přidání rozhraní Graph API klíč do aplikace

Blockchain Workbench používá Azure AD jako hlavní identity systém správy uživatelům interakci s blockchain aplikace. Aby Blockchain Workbench pro přístup k Azure AD a načíst informace o uživateli, jako jsou jména a e-mailů je nutné přidat přístupový klíč. Blockchain Workbench používá klíč k ověření s Azure AD.

1. Pro aplikaci jste zaregistrovali, vyberte **nastavení** v podokně podrobností zaregistrovanou aplikaci.
2. Vyberte **Klíče**.
3. Přidejte nový klíč zadáním klíče **popis** a výběr **vyprší platnost** hodnoty duration. 

    ![Vytvořit klíč](media/blockchain-workbench-deploy/app-key-create.png)

    |Nastavení  | Hodnota  |
    |---------|---------|
    | Popis | `Service` |
    | Platí do | Vyberte dobu trvání vypršení platnosti |

4. Vyberte **Uložit**. 
5. Zkopírujte hodnotu klíče a uloží jej pro pozdější. Je třeba pro nasazení.

    > [!IMPORTANT]
    >  Pokud nemáte uložit klíč pro nasazení, musíte vygenerovat nový klíč. Hodnota klíče nelze načíst později z portálu.

### <a name="get-application-id"></a>Získání ID aplikace

Informace ID a klienta aplikace jsou požadované pro nasazení. Shromažďovat a ukládat informace o použití během nasazení.

1. Pro aplikaci jste zaregistrovali, vyberte **nastavení** > **vlastnosti**.
2.  V **vlastnosti** podokně, kopírování a úložiště následující hodnoty pro pozdější použití při nasazení.

    ![Vlastnosti aplikace API](media/blockchain-workbench-deploy/app-properties.png)

    | Nastavení pro uložení  | Nasazení |
    |------------------|-------------------|
    | ID aplikace | Instalace nástroje Azure Active Directory > ID aplikace |

### <a name="get-tenant-domain-name"></a>Získat název domény klienta

Shromažďovat a ukládat název domény klienta služby Active Directory, kde jsou registrované aplikace. 

V levém navigačním podokně, vyberte **Azure Active Directory** služby. Vyberte **vlastní názvy domén**. Zkopírujte a uložte názvu domény.

![Název domény](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Nasazení Blockchain Workbench

Po dokončení požadovaných kroků jste připravení nasadit Blockchain Workbench. Následující oddíly popisují postup nasazení rozhraní.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  V horní pravé, že rohu a přepnete se do požadovaného Azure AD klienta, kde chcete nasadit Azure Blockchain Workbench vyberte svůj účet.
3.  V levém podokně vyberte **vytvořit prostředek**. Vyhledejte `Azure Blockchain Workbench` v **vyhledávání na webu Marketplace** panelu vyhledávání. 

    ![Panel hledání Marketplace.](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  Vyberte **Azure Blockchain Workbench**.

    ![Výsledky hledání Marketplace.](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  Vyberte **Vytvořit**.
5.  Dokončení základní nastavení.

    ![Vytvoření Azure Blockchain Workbench](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Předpona prostředků | Krátký jedinečný identifikátor pro vaše nasazení. Tato hodnota se používá jako základ pro názvy prostředků. |
    | Virtuální počítač uživatelské jméno | Uživatelské jméno se používá jako správce pro všechny virtuální počítače (VM). |
    | Typ ověřování | Vyberte, pokud chcete použít heslo nebo klíč pro připojení k virtuálním počítačům. |
    | Heslo | Heslo se používá pro připojení k virtuálním počítačům. |
    | SSH | Použijte veřejným klíčem RSA ve formátu jeden řádek začínající **ssh-rsa** nebo použijte formát PEM víceřádkový. Můžete vygenerovat pomocí klíče SSH `ssh-keygen` na Linuxu a OS X nebo PuTTYGen ve Windows. Další informace o klíče SSH, viz [klíče použití SSH se systémem Windows v Azure](../virtual-machines/linux/ssh-from-windows.md). |
    | Databáze heslo / potvrďte heslo k databázi | Zadejte heslo pro přístup k databázi vytvořené jako součást nasazení. |
    | Oblast nasazení | Zadejte, kam chcete nasadit Blockchain Workbench prostředky. Nejlepší dostupnost, mělo by to odpovídat **umístění** nastavení. |
    | Předplatné | Zadejte předplatné Azure, které chcete použít pro vaše nasazení. |
    | Skupiny prostředků | Vytvořit novou skupinu prostředků výběrem **vytvořit nový** a zadejte název skupiny prostředků jedinečný. |
    | Umístění | Zadejte oblast, kterou chcete nasadit rozhraní. |

6.  Vyberte **OK** ukončíte základní nastavení konfigurační oddíl.

7.  Dokončení **instalační program služby Azure Active Directory**.

    ![Instalace nástroje Azure AD](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Název domény | Použití Azure AD klienta shromážděny v [název domény klienta Get](#get-tenant-domain-name) část předpoklady v tématu. |
    | ID aplikace | Pomocí ID aplikace z aplikace registrace klienta Blockchain shromažďované [získání ID aplikace](#get-application-id) část předpoklady v tématu. |
    | Klíč aplikace | Použijte klíč aplikace z aplikace registrace klienta Blockchain shromážděny v [klíč přidat rozhraní Graph API k aplikaci](#add-graph-api-key-to-application) část předpoklady v tématu. |


8.  Klikněte na tlačítko **OK** ukončíte část konfigurace Azure AD parametry.

9.  Dokončení **sítě velikost a výkon** nastavení.

    ![Nastavení sítě a výkonu](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Počet uzlů blockchain | Zvolte počet Ethereum druhů validátoru uzlů nasadit ve vaší síti. |
    | Výkon úložiště | Vyberte upřednostňovaný výkon úložiště virtuálních počítačů ve vaší síti blockchain. |
    | Velikost virtuálního počítače | Vyberte upřednostňovanou velikost virtuálního počítače ve vaší síti blockchain. |

10. Vyberte **OK** ukončíte části velikost a výkon sítě.

11. Dokončení **Azure monitorování** nastavení.

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Monitorování | Zvolte, zda chcete povolit monitorování Azure, který chcete monitorovat vaši síť blockchain |
    | Připojte se k existující instanci analýzy protokolů | Zvolte, zda chcete použít existující analýzy protokolů instance nebo vytvořte novou. Pokud používáte existující instanci, zadejte své ID pracovního prostoru a primární klíč. |

12. Klikněte na tlačítko **OK** ukončíte části monitorování Azure.

13. Zkontrolujte souhrn k ověření, že parametry jsou správné.

    ![Souhrn](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. Vyberte **vytvořit** souhlas s podmínkami a nasadit vaši Azure Blockchain Workbench.

Nasazení může trvat až 90 minut. Na portálu Azure můžete sledovat průběh. V nově vytvořený prostředek skupiny, vyberte **nasazení > Přehled** zobrazíte stav nasazených artefakty.

## <a name="blockchain-workbench-web-url"></a>Adresa URL webové Blockchain Workbench

Po dokončení nasazení nástroje Blockchain Workbench novou skupinu prostředků obsahuje prostředky Blockchain Workbench. Blockchain Workbench služby jsou přístupné prostřednictvím adresu URL webu. Následující kroky ukazují, jak získat adresu URL webové rozhraní nasazené.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V levém navigačním podokně vyberte **skupiny prostředků**
3. Vyberte název skupiny prostředků, kterou jste zadali při nasazení Blockchain Workbench.
4. Klikněte **typ** záhlaví sloupce seřadíte seznam podle abecedy podle typu.
5. Existují dva prostředky s typem **služby App Service**. Vyberte prostředek typu **služby App Service** *bez* "-api" příponu.

    ![Seznam služeb aplikace](media/blockchain-workbench-deploy/resource-group-list.png)

6.  Ve službě App Service **Essentials** část, zkopírujte **URL** hodnotu, která představuje adresu URL webového vaše nasazené Blockchain Workbench.

    ![Aplikace služby essentials](media/blockchain-workbench-deploy/app-service.png)

Pokud chcete přiřadit vlastní název domény Blockchain Workbench, najdete v části [konfigurace vlastního názvu domény pro webovou aplikaci v Azure App Service pomocí Traffic Manager](../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Konfigurace adresa URL odpovědi

Po nasazený Azure Blockchain Workbench, dalším krokem je zajistit, klientská aplikace Azure Active Directory (Azure AD) je zaregistrován na správnou **adresa URL odpovědi** nástroje Blockchain Workbench nasazené webové adresy URL.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ověřte, zda že jsou v klientovi, kde registrovaná aplikace klienta Azure AD.
3. V levém navigačním podokně, vyberte **Azure Active Directory** služby. Vyberte **Registrace aplikací**.
4. Vyberte aplikaci klienta Azure AD, které je registrované v části požadavků.
5. Vyberte **Nastavení > odpovědět adresy URL**.
6. Zadejte adresu URL hlavní webové jste získali v nasazení Azure Blockchain Workbench **získat adresu URL webové Workbench Blockchain Azure** části. Adresa URL odpovědi je s předponou `https://`. Například `https://myblockchain2-7v75.azurewebsites.net`.

    ![Adresy URL odpovědí](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Vyberte **Uložit** aktualizace registrace klienta.

## <a name="next-steps"></a>Další postup

V tomto článku postupy jste nasadili Azure Blockchain Workbench. Chcete-li zjistit, jak vytvořit aplikaci blockchain, nadále další postupy článek.

> [!div class="nextstepaction"]
> [Vytvoření aplikace blockchain v Azure Blockchain Workbench](blockchain-workbench-create-app.md)