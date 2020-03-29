---
title: Nasazení Azure Blockchain Workbench Preview
description: Jak nasadit Azure Blockchain Workbench Preview
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 141bb8825e47eb2309f9f551990a2976e8f4e209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943206"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Nasazení Azure Blockchain Workbench Preview

Azure Blockchain Workbench Preview se nasazuje pomocí šablony řešení na Azure Marketplace. Šablona zjednodušuje nasazení komponent potřebných k vytvoření blockchainových aplikací. Po nasazení poskytuje Blockchain Workbench přístup ke klientským aplikacím pro vytváření a správu uživatelů a blockchainových aplikací.

Další informace o součástech blockchainworkbench najdete v tématu [Architektura Azure Blockchain Workbench](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Příprava nasazení

Blockchain Workbench umožňuje nasadit blockchainovou knihu spolu se sadou relevantních služeb Azure, které se nejčastěji používají k vytvoření aplikace založené na blockchainu. Nasazení Blockchain Workbench výsledky v následujících službách Azure zřizované v rámci skupiny prostředků ve vašem předplatném Azure.

* Plán služeb aplikace (standardní)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* Sql Database (Standard S0) + LOGICKÝ server SQL
* Účet úložiště Azure (standard LRS)
* Škálovací sada virtuálních strojů s kapacitou 1
* Skupina prostředků virtuální sítě (s nástrojem pro vyrovnávání zatížení, skupinou zabezpečení sítě, veřejnou IP adresou, virtuální sítí)
* Služba Azure Blockchain. Pokud používáte předchozí nasazení Blockchain Workbench, zvažte opětovné nasazení Azure Blockchain Workbench k použití služby Azure Blockchain.

Následuje příklad nasazení vytvořeného ve skupině prostředků **myblockchain.**

![Příklad nasazení](media/deploy/example-deployment.png)

Náklady na Blockchain Workbench je souhrn nákladů na základní služby Azure. Informace o cenách pro služby Azure lze vypočítat pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Požadavky

Azure Blockchain Workbench vyžaduje konfiguraci Azure AD a registrace aplikací. Můžete se rozhodnout provést [konfigurace](#azure-ad-configuration) Azure AD ručně před nasazením nebo spustit skript po nasazení. Pokud znovu nasazujete Blockchain Workbench, přečtěte si informace o [konfiguraci Azure](#azure-ad-configuration) AD.

> [!IMPORTANT]
> Workbench nemusí být nasazenve stejném tenantovi jako ten, který používáte k registraci aplikace Azure AD. Workbench musí být nasazen v tenantovi, kde máte dostatečná oprávnění k nasazení prostředků. Další informace o klientech Azure AD najdete v [tématu Jak získat klienta služby Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) a [integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Nasazení pracovní plochy blockchainu

Po dokončení nezbytných kroků jste připraveni nasadit blockchain workbench. V následujících částech jsou uvedeny, jak nasadit rozhraní.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte svůj účet v pravém horním rohu a přepněte na požadovaného klienta Azure AD, kde chcete nasadit Azure Blockchain Workbench.
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Vyberte **Blockchain** > **Blockchain Blockchain Workbench (preview)**.

    ![Vytvoření pracovní plochy Azure Blockchain](media/deploy/blockchain-workbench-settings-basic.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Předpona zdroje | Krátký jedinečný identifikátor pro vaše nasazení. Tato hodnota se používá jako základ pro pojmenování prostředků. |
    | Uživatelské jméno virtuálního virtuálního uživatele | Uživatelské jméno se používá jako správce pro všechny virtuální počítače (VM). |
    | Typ ověřování | Vyberte, pokud chcete použít heslo nebo klíč pro připojení k virtuálním počítačem. |
    | Heslo | Heslo se používá pro připojení k virtuálním počítačem. |
    | SSH | Použijte veřejný klíč RSA v jednořádkovém formátu začínajícím **ssh-rsa** nebo použijte víceřádkový formát PEM. Můžete generovat SSH `ssh-keygen` klíče pomocí na Linuxu a OS X, nebo pomocí PuTTYGen na Windows. Další informace o klíčích SSH najdete v [tématu Jak používat klíče SSH s Windows v Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Heslo pro databázi a blockchain | Zadejte heslo, které se má použít pro přístup k databázi vytvořené jako součást nasazení. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být mezi 12 & 72 znaků, 1 malý znak, 1 velký znak, 1 číslo a 1 speciální znak, který\`není znak čísla (#), percent(%), čárka(,), hvězda(*), zpětová citace( ), dvojité uvozovky(), jednoduché uvozovky('), pomlčka (-) a semicolumn(;) |
    | Oblast nasazení | Určete, kam se mají nasadit prostředky Blockchain Workbench. Pro nejlepší dostupnost by to mělo odpovídat nastavení **Umístění.** |
    | Předplatné | Zadejte předplatné Azure, které chcete použít pro vaše nasazení. |
    | Skupiny prostředků | Vytvořte novou skupinu prostředků tak, že vyberete **Vytvořit nový** a zadejte jedinečný název skupiny prostředků. |
    | Umístění | Zadejte oblast, kterou chcete nasadit rozhraní. |

1. Chcete-li dokončit oddíl konfigurace základního nastavení, vyberte **možnost OK.**

1. V **rozšířeném nastavení**zvolte, zda chcete vytvořit novou blockchainovou síť nebo použít existující blockchainovou síť proof-of-a-uthority.

    Pro **vytvořit nový**:

    Nová možnost *vytvořit* nasadí kvorum služby Azure Blockchain s výchozí základní sku.

    ![Pokročilá nastavení pro novou blockchainovou síť](media/deploy/advanced-blockchain-settings-new.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Cenová úroveň služby Azure Blockchain Service | Zvolte **základní** nebo **standardní** úroveň služby Azure Blockchain Service, která se používá pro blockchainworkbench |
    | Nastavení Azure Active Directory | Zvolte **Přidat později**.</br>Poznámka: Pokud jste se rozhodli [předem nakonfigurovat Azure AD](#azure-ad-configuration) nebo znovu nasazovat, zvolte *přidat nyní*. |
    | Výběr virtuálního zařízení | Vyberte preferovaný výkon úložiště a velikost virtuálního počítače pro vaši blockchainovou síť. Zvolte menší velikost virtuálního počítače, jako je *standardní DS1 v2,* pokud máte předplatné s nízkými limity služeb, jako je bezplatná úroveň Azure. |

    Pro **použití existující**:

    *Stávající možnost použití* umožňuje zadat blockchainovou síť Ethereum Proof-of-Authority (PoA). Koncové body mají následující požadavky.

   * Koncovým bodem musí být blockchainová síť Ethereum Proof-of-Authority (PoA).
   * Koncový bod musí být veřejně přístupný v síti.
   * Blockchainová síť PoA by měla být nakonfigurována tak, aby cena plynu byla nastavena na nulu.

     > [!NOTE]
     > Blockchain Workbench účty nejsou financovány. Pokud jsou požadovány finanční prostředky, transakce se nezdaří.

     ![Pokročilá nastavení pro stávající blockchainovou síť](media/deploy/advanced-blockchain-settings-existing.png)

     | Nastavení | Popis  |
     |---------|--------------|
     | Koncový bod RPC etherea | Poskytněte koncový bod Vzdáleného volání procedur existující blockchainové sítě PoA. Koncový bod začíná https:// nebo http:// a končí číslem portu. Například `http<s>://<network-url>:<port>`. |
     | Nastavení Azure Active Directory | Zvolte **Přidat později**.</br>Poznámka: Pokud jste se rozhodli [předem nakonfigurovat Azure AD](#azure-ad-configuration) nebo znovu nasazovat, zvolte *přidat nyní*. |
     | Výběr virtuálního zařízení | Vyberte preferovaný výkon úložiště a velikost virtuálního počítače pro vaši blockchainovou síť. Zvolte menší velikost virtuálního počítače, jako je *standardní DS1 v2,* pokud máte předplatné s nízkými limity služeb, jako je bezplatná úroveň Azure. |

1. Chcete-li dokončit upřesňující nastavení, vyberte **ok.**

1. Projděte si souhrn a ověřte, zda jsou vaše parametry přesné.

    ![Souhrn](media/deploy/blockchain-workbench-summary.png)

1. Vyberte **Vytvořit,** chcete-li souhlasit s podmínkami a nasadit Azure Blockchain Workbench.

Nasazení může trvat až 90 minut. K monitorování průběhu můžete použít portál Azure. V nově vytvořené skupině prostředků vyberte **Nasazení > Přehled,** chcete-li zobrazit stav nasazených artefaktů.

> [!IMPORTANT]
> Po nasazení je třeba dokončit nastavení služby Active Directory. Pokud jste **zvolili Přidat později**, musíte spustit [konfigurační skript Azure AD](#azure-ad-configuration-script).  Pokud jste zvolili **Přidat nyní**, je třeba [nakonfigurovat adresu URL pro odpověď](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench webová adresa URL

Po dokončení nasazení blockchainworkbench obsahuje nová skupina prostředků blockchain workbench vaše prostředky Blockchain Workbench. Služby Blockchain Workbench jsou přístupné prostřednictvím webové adresy URL. Následující kroky ukazují, jak načíst webovou adresu URL nasazeného rozhraní.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levém navigačním podokně vyberte **položku Skupiny prostředků**.
1. Zvolte název skupiny prostředků, který jste zadali při nasazování Blockchain Workbench.
1. Vyberte záhlaví sloupce **TYP,** chcete-li seznam seřadit abecedně podle typu.
1. Existují dva prostředky s typem **App Service**. Vyberte prostředek typu **App Service** *bez* přípony "-api".

    ![Seznam služeb aplikace](media/deploy/resource-group-list.png)

1. V **přehledu**služby App Service zkopírujte hodnotu **URL,** která představuje webovou adresu URL na nasazený blockchain workbench.

    ![Základy služby App Service](media/deploy/app-service.png)

Pokud chcete přidružit vlastní název domény k Blockchain Workbench, přečtěte si [informace o konfiguraci vlastního názvu domény pro webovou aplikaci ve službě Azure App Service pomocí Traffic Manageru](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Konfigurační skript Azure AD

Azure AD musí být nakonfigurované k dokončení nasazení Blockchain Workbench. Ke konfiguraci použijete skript prostředí PowerShell.

1. V prohlížeči přejděte na [webovou adresu BLOCKCHAIN Workbench .](#blockchain-workbench-web-url)
1. Zobrazí se pokyny k nastavení Azure AD pomocí Cloud Shellu. Zkopírujte příkaz a spusťte Cloud Shell.

    ![Spuštění skriptu AAD](media/deploy/launch-aad-script.png)

1. Zvolte klienta Azure AD, do kterého jste nasadili Blockchain Workbench.
1. V prostředí Cloud Shell PowerShell vložte a spusťte příkaz.
1. Po zobrazení výzvy zadejte klienta Azure AD, který chcete použít pro Blockchain Workbench. Bude to nájemce obsahující uživatele pro Blockchain Workbench.

    > [!IMPORTANT]
    > Ověřený uživatel vyžaduje oprávnění k vytváření registrací aplikací Azure AD a udělení delegovaných oprávnění aplikací v tenantovi. Možná budete muset požádat správce klienta o spuštění konfiguračního skriptu Azure AD nebo vytvoření nového klienta.

    ![Zadejte klienta Azure AD](media/deploy/choose-tenant.png)

1. Budete vyzváni k ověření klienta Azure AD pomocí prohlížeče. Otevřete webovou adresu URL v prohlížeči, zadejte kód a ověřte.

    ![Ověření pomocí kódu](media/deploy/authenticate.png)

1. Skript vydává několik stavových zpráv. Pokud byl klient úspěšně zřízen, zobrazí se zpráva o stavu **ÚSPĚCH.**
1. Přejděte na adresu URL blockchainworkbench. Budete vyzváni k udělení oprávnění ke čtení adresáře. To umožňuje přístup webové aplikace Blockchain Workbench uživatelům v tenantovi. Pokud jste správcem klienta, můžete souhlasit pro celou organizaci. Tato možnost přijímá souhlas pro všechny uživatele v tenantovi. V opačném případě je každý uživatel vyzván k udělení souhlasu při prvním použití webové aplikace Blockchain Workbench.
1. **Chcete-li souhlasit,** vyberte možnost Přijmout.

     ![Souhlas se čtením profilů uživatelů](media/deploy/graph-permission-consent.png)

1. Po souhlasu lze použít webovou aplikaci Blockchain Workbench.

Dokončili jste nasazení Azure Blockchain Workbench. V [dalším tématu](#next-steps) najdete návrhy, jak začít používat nasazení.

## <a name="azure-ad-configuration"></a>Konfigurace Azure AD

Pokud se rozhodnete ručně nakonfigurovat nebo ověřit nastavení Azure AD před nasazením, proveďte všechny kroky v této části. Pokud dáváte přednost automatické konfiguraci nastavení Azure AD, použijte [konfigurační skript Azure AD](#azure-ad-configuration-script) po nasazení Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registrace aplikace Blockchain Workbench API

Nasazení Blockchain Workbench vyžaduje registraci aplikace Azure AD. K registraci aplikace potřebujete klienta Azure Active Directory (Azure AD). Můžete použít existujícího klienta nebo vytvořit nového klienta. Pokud používáte existující klienta Azure AD, potřebujete dostatečná oprávnění k registraci aplikací, udělení oprávnění rozhraní API graphu a povolení přístupu hosta v rámci klienta Azure AD. Pokud nemáte dostatečná oprávnění v existujícím tenantovi Azure AD, vytvořte nového klienta.


1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte svůj účet v pravém horním rohu a přepněte na požadovaný klient Azure AD. Tenant by měl být tenantem správce předplatného, kde se nasazuje Azure Blockchain Workbench a máte dostatečná oprávnění k registraci aplikací.
1. V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Registrace** > aplikací**Nová registrace**.

    ![Registrace aplikací](media/deploy/app-registration.png)

1. Zadejte **zobrazovaný název** a zvolte **účty pouze v tomto organizačním adresáři**.

    ![Vytvoření registrace aplikace](media/deploy/app-registration-create.png)

1. Chcete-li zaregistrovat aplikaci Azure AD, vyberte **možnost Registrovat.**

### <a name="modify-manifest"></a>Změnit manifest

Dále je třeba upravit manifest pro použití rolí aplikací v rámci Azure AD k určení správců Blockchain Workbench.  Další informace o manifestech aplikací naleznete v [tématu Manifest aplikace Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).


1. Identifikátor GUID je vyžadován pro manifest. Identifikátor GUID můžete generovat pomocí `[guid]::NewGuid()` příkazu nebo `New-GUID` rutiny prostředí PowerShell. Další možností je použít guid generátor webové stránky.
1. U aplikace, kterou jste zaregistrovali, vyberte **manifest** v části **Spravovat.**
1. Dále aktualizujte část **appRoles** manifestu. Nahraďte `"appRoles": []` dodaným JSON. Nezapomeňte nahradit hodnotu pole **id** vygenerovaným identifikátorem. 

    ![Upravit manifest](media/deploy/edit-manifest.png)

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

    > [!IMPORTANT]
    > Hodnota **Správce** je potřebná k identifikaci správců Blockchain Workbench.

1. V manifestu také změňte **hodnotu Oauth2AllowImplicitFlow** na **hodnotu true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Vyberte **Uložit,** chcete-li uložit změny manifestu.

### <a name="add-graph-api-required-permissions"></a>Přidání oprávnění požadovaných rozhraním API pro graf

Aplikace rozhraní API musí požádat uživatele o oprávnění pro přístup k adresáři. Nastavte následující požadovaná oprávnění pro aplikaci rozhraní API:

1. V registraci aplikace *Blockchain API* vyberte oprávnění **rozhraní API**. Ve výchozím nastavení je přidáno oprávnění rozhraní API rozhraní **Graph.**
1. Aplikace Workbench vyžaduje přístup pro čtení k základním informacím o profilu uživatelů. V *části Nakonfigurovaná oprávnění*vyberte Přidat **oprávnění**. V **rozhraní microsoft api**vyberte Microsoft **Graph**.
1. Vzhledem k tomu, že aplikace Workbench používá pověření ověřeného uživatele, vyberte **delegovaná oprávnění**.
1. V kategorii *Uživatel* zvolte **Oprávnění User.ReadBasic.All.**

    ![Konfigurace registrace aplikace Azure AD zobrazující přidání oprávnění delegáta microsoft graphu User.ReadBasic.All](media/deploy/add-graph-user-permission.png)

    Vyberte **Přidat oprávnění**.

1. V *části Nakonfigurovaná oprávnění*vyberte **Udělit souhlas správce** pro doménu a pro výzvu k ověření vyberte **Ano.**

   ![Udělení oprávnění](media/deploy/client-app-grant-permissions.png)

   Udělení oprávnění umožňuje blockchainworkbench přístup k uživatelům v adresáři. Oprávnění ke čtení je vyžadováno pro vyhledávání a přidání členů do Blockchain Workbench.

### <a name="get-application-id"></a>Získat ID aplikace

ID aplikace a informace o klientovi jsou vyžadovány pro nasazení. Shromažďujte a uklápěte informace pro použití během nasazení.

1. Pro aplikaci, kterou jste zaregistrovali, vyberte **Přehled**.
1. Zkopírujte a uložte hodnotu **ID aplikace** pro pozdější použití během nasazení.

    ![Vlastnosti aplikace API](media/deploy/app-properties.png)

    | Nastavení pro uložení  | Použití v nasazení |
    |------------------|-------------------|
    | ID aplikace (klienta) | Nastavení služby Azure Active Directory > ID aplikace |

### <a name="get-tenant-domain-name"></a>Získání názvu domény klienta

Shromážděte a uložte název domény klienta služby Active Directory, kde jsou aplikace registrovány. 

V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Názvy vlastních domén**. Zkopírujte a uložte název domény.

![Název domény](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Nastavení uživatele typu Host

Pokud máte ve svém tenantovi Azure AD uživatele typu Host, postupujte podle dalších kroků, abyste zajistili, že přiřazení a správa uživatelů blockchainworkbench funguje správně.

1. Přepněte si svého klienta Azure AD a vyberte **Nastavení Azure Active Directory > uživatele > Spravovat nastavení externí spolupráce**.
1. Nastavit **oprávnění uživatele typu Host jsou omezena** na **ne**.
    ![Nastavení externí spolupráce](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurace adresy URL odpovědi

Po nasazení Azure Blockchain Workbench je nutné nakonfigurovat klientskou aplikaci Azure Active Directory (Azure AD) **odpověď URL** nasazené webové adresy URL Blockchain Workbench.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Ověřte, že jste v tenantovi, kde jste zaregistrovali klientskou aplikaci Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Registrace aplikací**.
1. V yberte klientskou aplikaci Azure AD, kterou jste zaregistrovali v části požadavky.
1. Vyberte **Ověřování**.
1. Zadejte hlavní webovou adresu URL nasazení Azure Blockchain Workbench, které jste načetli v části [WebOVÁ ADRESA URL Blockchain Workbench.](#blockchain-workbench-web-url) Adresa URL odpovědi je `https://`předpona s . Například `https://myblockchain2-7v75.azurewebsites.net`.

    ![Adresy URL odpovědí na ověřování](media/deploy/configure-reply-url.png)

1. V části **Upřesnit nastavení** zaškrtněte **políčko Přístupové tokeny** a **tokeny ID**.

    ![Upřesňující nastavení ověřování](media/deploy/authentication-advanced-settings.png)

1. Chcete-li aktualizovat registraci klienta, vyberte **možnost Uložit.**

## <a name="remove-a-deployment"></a>Odebrání nasazení

Když nasazení už není potřeba, můžete nasazení odebrat odstraněním skupiny prostředků Blockchain Workbench.

1. Na webu Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit. 
1. Vyberte **Odstranit skupinu prostředků**. Ověřte odstranění zadáním názvu skupiny prostředků a vyberte **odstranit**.

    ![Odstranění skupiny prostředků](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Další kroky

V tomto článku s návody jste nasadili Azure Blockchain Workbench. Chcete-li se dozvědět, jak vytvořit blockchainovou aplikaci, pokračujte dalším článkem s návody.

> [!div class="nextstepaction"]
> [Vytvoření blockchainové aplikace v Azure Blockchain Workbench](create-app.md)
