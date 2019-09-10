---
title: Nasadit Azure blockchain Workbench Preview
description: Jak nasadit Azure blockchain Workbench Preview
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 2ea18c784c6b5cf61013c131360d20349e67b1e5
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845275"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Nasadit Azure blockchain Workbench Preview

Azure blockchain Workbench Preview je nasazená pomocí šablony řešení v Azure Marketplace. Šablona zjednodušuje nasazení komponent potřebných k vytváření blockchainch aplikací. Po nasazení aplikace blockchain Workbench poskytuje přístup k klientským aplikacím pro vytváření a správu uživatelů a blockchain aplikací.

Další informace o komponentách aplikace blockchain Workbench najdete v tématu [Architektura Azure blockchain Workbench](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Příprava nasazení

Blockchain Workbench umožňuje nasadit Blockchainovou knihu spolu se sadou relevantních služeb Azure nejčastěji používaných k vytváření aplikací založených na blockchain. Nasazení aplikace blockchain Workbench vede ke zřízení následujících služeb Azure v rámci skupiny prostředků ve vašem předplatném Azure.

* Plán App Service (Standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL Database (standardní S0) + logický server SQL
* Účet Azure Storage (Standard LRS)
* Sada škálování virtuálního počítače s kapacitou 1
* Virtual Network skupiny prostředků (s Load Balancer, skupinou zabezpečení sítě, veřejnou IP adresou, Virtual Network)
* Služba Azure blockchain Pokud používáte předchozí nasazení aplikace blockchain Workbench, zvažte opětovné nasazení Azure blockchain Workbench pro použití služby Azure blockchain Service.

Následuje příklad nasazení vytvořeného ve skupině prostředků **myblockchain** .

![Příklad nasazení](media/deploy/example-deployment.png)

Cena blockchain Workbench je souhrnem nákladů na příslušné služby Azure. Informace o cenách služeb Azure se dají vypočítat pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Požadavky

Azure blockchain Workbench vyžaduje konfiguraci a registraci aplikací Azure AD. Konfigurace služby Azure AD můžete před nasazením nebo spuštěním skriptu po nasazení zvolit [ručně](#azure-ad-configuration) . Pokud blockchain Workbench znovu nasazujete, přečtěte si článek [konfigurace Azure AD](#azure-ad-configuration) a ověřte konfiguraci služby Azure AD.

> [!IMPORTANT]
> Workbench není nutné nasazovat do stejného tenanta jako ten, který používáte k registraci aplikace služby Azure AD. Aplikace Workbench musí být nasazená v tenantovi, kde máte dostatečná oprávnění k nasazení prostředků. Další informace o klientech Azure AD najdete v tématu [Jak získat tenanta služby Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) a [integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Nasazení aplikace blockchain Workbench

Po dokončení požadovaných kroků jste připraveni k nasazení aplikace blockchain Workbench. Následující části popisují způsob nasazení rozhraní.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte svůj účet a přepněte na požadovaného tenanta Azure AD, kde chcete nasadit Azure blockchain Workbench.
3. V levém podokně vyberte **vytvořit prostředek**. Na panelu hledání na **webu Marketplace vyhledejte.** `Azure Blockchain Workbench` 

    ![Panel hledání Marketplace](media/deploy/marketplace-search-bar.png)

4. Vyberte **Azure blockchain Workbench**.

    ![Výsledky hledání na Marketplace](media/deploy/marketplace-search-results.png)

5. Vyberte **Vytvořit**.
6. Dokončete základní nastavení.

    ![Vytvoření aplikace Azure blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Předpona prostředku | Krátký jedinečný identifikátor pro vaše nasazení. Tato hodnota se používá jako základ pro pojmenování prostředků. |
    | Uživatelské jméno virtuálního počítače | Uživatelské jméno se používá jako správce pro všechny virtuální počítače (VM). |
    | Typ ověřování | Tuto možnost vyberte, pokud chcete pro připojení k virtuálním počítačům použít heslo nebo klíč. |
    | Heslo | Heslo se používá pro připojení k virtuálním počítačům. |
    | SSH | Použijte veřejný klíč RSA v jednořádkovém formátu počínaje **protokolem SSH-RSA** nebo použijte víceřádkový formát PEM. Klíče SSH můžete generovat pomocí `ssh-keygen` v systémech Linux a OS X nebo pomocí PuTTYGen ve Windows. Další informace o klíčích SSH najdete v tématu [použití klíčů ssh s Windows v Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Heslo databáze a blockchain | Zadejte heslo, které se má použít pro přístup k databázi vytvořené jako součást nasazení. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslice a 1 speciální znak, který není číslo znaku (#), procenta (%), čárka (,), hvězdička (*), návratová uvozovka. (\`), dvojité uvozovky ("), jednoduché uvozovky ('), pomlčky (-) a semicolumn (;) |
    | Oblast nasazení | Určete, kam se mají nasazovat prostředky blockchain Workbench. Pro zajištění nejlepší dostupnosti by se měla shodovat s nastavením **umístění** . |
    | Subscription | Zadejte předplatné Azure, které chcete použít pro nasazení. |
    | Skupiny prostředků | Vytvořte novou skupinu prostředků tak, že vyberete **vytvořit novou** a zadáte jedinečný název skupiny prostředků. |
    | Location | Určete oblast, do které chcete nasadit rozhraní. |

7. Výběrem **OK** dokončete oddíl konfigurace základního nastavení.

8. V části **Upřesnit nastavení**vyberte, jestli chcete vytvořit novou blockchain síť, nebo použijte stávající síť blockchain pro ověření.

    Pro **Vytvoření nové**:

    Možnost *vytvořit novou* nasadí hlavní knihu kvora služby Azure blockchain s výchozí hodnotou Basic SKU.

    ![Pokročilá nastavení pro novou blockchain síť](media/deploy/advanced-blockchain-settings-new.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Cenová úroveň služby Azure blockchain | Vyberte úroveň služby Azure blockchain úrovně **Basic** nebo **Standard** , která se používá pro blockchain Workbench. |
    | Nastavení Azure Active Directory | Vyberte **přidat později**.</br>Poznámka: Pokud se rozhodnete [předkonfigurovat službu Azure AD](#azure-ad-configuration) nebo znovu nasazovat, zvolte možnost *Přidat nyní*. |
    | Výběr virtuálního počítače | Vyberte preferovaný výkon úložiště a velikost virtuálního počítače pro vaši blockchain síť. Pokud jste v předplatném s nízkou úrovní služeb, jako je Azure Free, vyberte menší velikost virtuálního počítače, jako je třeba *Standard DS1 v2* . |

    Pro **použít existující**:

    Možnost *použít existující* umožňuje zadat ethereem síť blockchain pro ověření (Poa). Koncové body mají následující požadavky.

   * Koncový bod musí být Ethereem síť blockchain pro ověření kontrolního úřadu (PoA).
   * Koncový bod musí být prostřednictvím sítě veřejně přístupný.
   * Síť PoA blockchain by měla být nakonfigurovaná tak, aby měla cena za plyn nastavenou na nulu.

     > [!NOTE]
     > Účty blockchain Workbench nejsou financovány. Pokud jsou prostředky požadovány, transakce selžou.

     ![Pokročilá nastavení pro stávající blockchain síť](media/deploy/advanced-blockchain-settings-existing.png)

     | Nastavení | Popis  |
     |---------|--------------|
     | Koncový bod ethereem RPC | Zadejte koncový bod vzdáleného volání procedur (RPC) existující sítě blockchain pro PoA. Koncový bod začíná na https://nebo http://a končí číslem portu. Například `http<s>://<network-url>:<port>`. |
     | Nastavení Azure Active Directory | Vyberte **přidat později**.</br>Poznámka: Pokud se rozhodnete [předkonfigurovat službu Azure AD](#azure-ad-configuration) nebo znovu nasazovat, zvolte možnost *Přidat nyní*. |
     | Výběr virtuálního počítače | Vyberte preferovaný výkon úložiště a velikost virtuálního počítače pro vaši blockchain síť. Pokud jste v předplatném s nízkou úrovní služeb, jako je Azure Free, vyberte menší velikost virtuálního počítače, jako je třeba *Standard DS1 v2* . |

9. Výběrem **OK** dokončete Rozšířená nastavení.

10. Zkontrolujte souhrn a ověřte správnost parametrů.

    ![Souhrn](media/deploy/blockchain-workbench-summary.png)

11. Vyberte **vytvořit** , pokud souhlasíte s podmínkami a nasadíte si Azure blockchain Workbench.

Nasazení může trvat až 90 minut. Pomocí Azure Portal můžete monitorovat průběh. V nově vytvořené skupině prostředků vyberte **nasazení > přehled** a zobrazte stav nasazených artefaktů.

> [!IMPORTANT]
> Po nasazení je potřeba dokončit nastavení služby Active Directory. Pokud jste zvolili možnost **přidat později**, budete muset spustit [konfigurační skript Azure AD](#azure-ad-configuration-script).  Pokud jste zvolili možnost **Přidat nyní**, je nutné [nakonfigurovat adresu URL odpovědi](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Webová adresa URL aplikace blockchain Workbench

Po dokončení nasazení aplikace blockchain Workbench obsahuje nová skupina prostředků vaše prostředky blockchain Workbench. Služby blockchain Workbench se přistupují prostřednictvím webové adresy URL. Následující kroky ukazují, jak načíst webovou adresu URL nasazeného rozhraní.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V levém navigačním podokně vyberte **skupiny prostředků** .
3. Vyberte název skupiny prostředků, který jste zadali při nasazování aplikace blockchain Workbench.
4. Vyberte záhlaví sloupce **typ** k seřazení seznamu abecedně podle typu.
5. Existují dva prostředky s typem **App Service**. Vyberte prostředek typu **App Service** *bez* přípony "-API".

    ![Seznam služby App Service](media/deploy/resource-group-list.png)

6. V části App Service **Essentials** Zkopírujte hodnotu **URL** , která představuje webovou adresu URL vaší nasazené aplikace blockchain Workbench.

    ![Základy App Service](media/deploy/app-service.png)

Informace o přidružení vlastního názvu domény k blockchain Workbench najdete v tématu [Konfigurace vlastního názvu domény pro webovou aplikaci v Azure App Service pomocí Traffic Manager](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Konfigurační skript Azure AD

Aby bylo možné dokončit nasazení aplikace blockchain Workbench, musí být služba Azure AD nakonfigurovaná. Ke konfiguraci můžete použít skript prostředí PowerShell.

1. V prohlížeči přejděte na [adresu URL webu blockchain Workbench](#blockchain-workbench-web-url).
2. Zobrazí se pokyny k nastavení Azure AD pomocí Cloud Shell. Zkopírujte příkaz a spusťte Cloud Shell.

    ![Spustit skript AAD](media/deploy/launch-aad-script.png)

3. Vyberte tenanta Azure AD, kde jste nasadili blockchain Workbench.
4. V Cloud Shell vložte a spusťte příkaz.
5. Po zobrazení výzvy zadejte tenanta Azure AD, kterého chcete použít pro blockchain Workbench. To bude tenant, který obsahuje uživatele pro blockchain Workbench.

    > [!IMPORTANT]
    > Ověřený uživatel vyžaduje oprávnění k vytváření registrací aplikací Azure AD a udělení oprávnění delegované aplikace v tenantovi. Možná budete muset požádat správce tenanta, aby spustil konfigurační skript Azure AD, nebo vytvořit nového tenanta.

    ![Zadejte tenanta Azure AD.](media/deploy/choose-tenant.png)

6. Budete vyzváni k ověření v tenantovi Azure AD pomocí prohlížeče. Otevřete webovou adresu URL v prohlížeči, zadejte kód a ověřte.

    ![Ověřování pomocí kódu](media/deploy/authenticate.png)

7. Skript vypíše několik stavových zpráv. Pokud se tenant úspěšně zřídil, zobrazí se chybová zpráva o úspěšném stavu.
8. Přejděte na adresu URL aplikace blockchain Workbench. Zobrazí se výzva ke souhlasu s udělením oprávnění ke čtení do adresáře. Díky tomu může webová aplikace blockchain Workbench přistupovat k uživatelům v tenantovi. Pokud jste správcem tenanta, můžete si vybrat souhlas s celou organizací. Tato možnost přijímá souhlas všech uživatelů v tenantovi. V opačném případě se každému uživateli zobrazí výzva k vyjádření souhlasu při prvním použití webové aplikace blockchain Workbench.
9. Vyberte **přijmout** k souhlasu.

     ![Souhlas se čtením profilů uživatelů](media/deploy/graph-permission-consent.png)

10. Po vyjádření souhlasu se dá použít webová aplikace blockchain Workbench.

## <a name="azure-ad-configuration"></a>Konfigurace Azure AD

Pokud se rozhodnete nakonfigurovat nastavení Azure AD před nasazením ručně nebo ověřit, proveďte všechny kroky v této části. Pokud dáváte přednost automatické konfiguraci nastavení Azure AD, použijte [konfigurační skript Azure AD](#azure-ad-configuration-script) po nasazení aplikace blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registrace aplikace API kolekce Blockchain Workbench

Nasazení aplikace blockchain Workbench vyžaduje registraci aplikace služby Azure AD. K registraci aplikace potřebujete klienta Azure Active Directory (Azure AD). Můžete použít existujícího tenanta nebo vytvořit nového tenanta. Pokud používáte existujícího tenanta Azure AD, potřebujete dostatečná oprávnění k registraci aplikací, udělení Graph API oprávnění a povolení přístupu hostů v rámci tenanta Azure AD. Pokud nemáte dostatečná oprávnění v existujícím tenantovi Azure AD, vytvořte nového tenanta.


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte svůj účet a přepněte na požadovaného tenanta Azure AD. Tenant by měl být tenant Správce předplatného, kde je aplikace Workbench nasazená a máte dostatečná oprávnění k registraci aplikací.
3. V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Registrace aplikací** > **Registrace nové aplikace**.

    ![Registrace aplikace](media/deploy/app-registration.png)

4. Zadejte **název** a **přihlašovací adresu URL** pro aplikaci. Můžete použít zástupné hodnoty, protože hodnoty jsou během nasazení změněny. 

    ![Vytvořit registraci aplikace](media/deploy/app-registration-create.png)

    |Nastavení  | Value  |
    |---------|---------|
    |Name | `Blockchain API` |
    |Typ aplikace |Webová aplikace/rozhraní API|
    |Přihlašovací adresa URL | `https://blockchainapi` |

5. Vyberte **vytvořit** a zaregistrujte aplikaci Azure AD.

### <a name="modify-manifest"></a>Upravit manifest

Dál je potřeba upravit manifest tak, aby používal aplikační role v rámci Azure AD, a zadat správce blockchain Workbench.  Další informace o manifestech aplikací naleznete v tématu [Azure Active Directory manifest aplikace](../../active-directory/develop/reference-app-manifest.md).

1. V případě aplikace, kterou jste zaregistrovali, vyberte v podokně podrobností registrované aplikace možnost **manifest** .
2. Vygenerujte identifikátor GUID. Identifikátor GUID můžete vygenerovat pomocí příkazu PowerShellu [GUID]:: Rutina NewGuid () nebo New-GUID Další možností je použít web generátoru GUID.
3. Chystáte se aktualizovat část **appRoles** manifestu. V podokně upravit manifest vyberte **Upravit** a nahradit `"appRoles": []` zadaným kódem JSON. Nezapomeňte nahradit hodnotu pole **ID** identifikátorem GUID, který jste vygenerovali. 

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
    > **Správce** hodnoty je nutný k identifikaci správců aplikace blockchain Workbench.

4. V manifestu také změňte hodnotu **Oauth2AllowImplicitFlow** na **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Vyberte **Uložit** a uložte změny manifestu.

### <a name="add-graph-api-required-permissions"></a>Přidat Graph API požadovaná oprávnění

Aplikace API musí pro přístup k adresáři požádat o oprávnění od uživatele. Nastavte následující požadované oprávnění pro aplikaci API:

1. V registraci aplikace blockchain API vyberte **nastavení > požadovaná oprávnění > vyberte rozhraní API > Microsoft Graph**.

    ![Vyberte rozhraní API.](media/deploy/client-app-select-api.png)

    Klikněte na tlačítko **vyberte**.

2. V možnosti **Povolit přístup** v části **delegovaná oprávnění**vyberte **číst základní profily všech uživatelů**.

    ![Povolit přístup](media/deploy/client-app-read-perms.png)

    Vyberte **Uložit** a potomvyberte Hotovo.

3. V části **požadovaná oprávnění**vyberte **udělit oprávnění** a pak u výzvy k ověření vyberte **Ano** .

   ![Udělení oprávnění](media/deploy/client-app-grant-permissions.png)

   Udělení oprávnění umožňuje blockchain Workbench získat přístup k uživatelům v adresáři. K vyhledávání a přidávání členů do aplikace blockchain Workbench se vyžaduje oprávnění ke čtení.

### <a name="get-application-id"></a>Získat ID aplikace

Pro nasazení jsou vyžadovány informace o ID aplikace a tenantovi. Shromažďovat a ukládat informace, které se mají použít během nasazení

1. U aplikace, kterou jste zaregistrovali, vyberte **Nastavení** > **vlastnosti**.
2. V podokně **vlastnosti** zkopírujte a uložte následující hodnoty pro pozdější použití během nasazování.

    ![Vlastnosti aplikace API](media/deploy/app-properties.png)

    | Nastavení do úložiště  | Použít v nasazení |
    |------------------|-------------------|
    | ID aplikace | Azure Active Directory nastavení > ID aplikace |

### <a name="get-tenant-domain-name"></a>Získat název domény tenanta

Shromážděte a uložte název domény tenanta služby Active Directory, ve které jsou aplikace zaregistrované. 

V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Názvy vlastních domén**. Zkopírujte a uložte název domény.

![Název domény](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Uživatelská nastavení typu Host

Pokud máte uživatele typu Host ve vašem tenantovi Azure AD, postupujte podle dalších kroků a ujistěte se, že přiřazování a Správa uživatelů blockchain Workbench funguje správně.

1. Přepněte svého tenanta Azure AD a vyberte **Azure Active Directory > nastavení uživatele > spravovat nastavení externích spolupráce**.
2. Nastavení **oprávnění uživatele hosta jsou omezená** na **ne**.
    ![Nastavení externí spolupráce](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurace adresy URL odpovědi

Po nasazení aplikace Azure blockchain Workbench musíte nakonfigurovat **adresu URL odpovědi** klientské aplikace Azure Active Directory (Azure AD) na NASAZENOU adresu URL webu blockchain Workbench.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ověřte, že jste v tenantovi, kde jste zaregistrovali klientskou aplikaci Azure AD.
3. V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Registrace aplikací**.
4. Vyberte klientskou aplikaci Azure AD, kterou jste zaregistrovali v části požadavky.
5. Vyberte **nastavení > adresy URL odpovědí**.
6. Zadejte adresu URL hlavního webu nasazení Azure blockchain Workbench, kterou jste získali v části **získání webové adresy URL Azure blockchain Workbench** . Adresa URL odpovědi má předponu `https://`. Například `https://myblockchain2-7v75.azurewebsites.net`.

    ![Adresy URL pro odpověď](media/deploy/configure-reply-url.png)

7. Kliknutím na **Uložit** aktualizujte registraci klienta.

## <a name="remove-a-deployment"></a>Odebrání nasazení

Pokud už nasazení nepotřebujete, můžete nasazení odebrat odstraněním skupiny prostředků blockchain Workbench.

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit. 
2. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

    ![Odstranit skupinu prostředků](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Další postup

V tomto článku se naučíte nasadit Azure blockchain Workbench. Pokud se chcete dozvědět, jak vytvořit aplikaci blockchain, přejděte k dalšímu článku s postupem.

> [!div class="nextstepaction"]
> [Vytvoření aplikace v blockchain v Azure blockchain Workbench](create-app.md)
