---
title: Nasadit Azure Blockchain Workbench
description: Jak nasadit Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: fcba3aef29e1566f9dfb2b151c15fe683be94fdb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266583"
---
# <a name="deploy-azure-blockchain-workbench"></a>Nasadit Azure Blockchain Workbench

Azure Blockchain Workbench se nasadí pomocí šablony řešení na webu Azure Marketplace. Šablona zjednodušuje nasazení součásti potřebné k vytvoření blockchainové aplikace. Po nasazení Blockchain Workbench poskytuje přístup ke klientské aplikace k vytváření a správě uživatelů a blockchainové aplikace.

Další informace o komponentách Blockchain Workbench najdete v tématu [architektury Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení

Blockchain Workbench umožňuje nasadit blockchain účetní kniha společně se sadou relevantní služby Azure, které jsou nejčastěji používají k vytvoření blockchainové aplikace. Nasazení Blockchain Workbench výsledkem v rámci skupiny prostředků ve vašem předplatném Azure zřídí následující služby Azure.

* Téma 1 event gridu
* Namespace 1 Service Bus
* 1 Application Insights
* 1 SQL Database (Standard S0)
* 2 App Services (Standard)
* 2 trezory klíčů azure
* 2 účtů služby azure Storage (Standard LRS)
* 2 škálovací sady virtuálních počítačů (pro program pro ověření a pracovní uzly)
* 2 virtuální sítě (včetně nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě a veřejnou IP adresu pro každou virtuální síť)
* Volitelné: Azure Monitor

Tady je příklad nasazení vytvořit v **myblockchain** skupinu prostředků.

![Příklad nasazení](media/deploy/example-deployment.png)

Náklady na Blockchain Workbench není agregovaný a nákladů na podpůrné služby Azure. Informace o cenách pro Azure services je možné vypočítat pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/).

> [!IMPORTANT]
> Pokud používáte předplatné s omezení s nízkou služeb, jako je předplatné Azure na úrovni free, může nasazení selhat z důvodu Nedostatečná kvóta jader virtuálního počítače. Před nasazením, zkontrolujte pomocí pokynů od kvótu [kvóty virtuálních procesorů virtuálního počítače](../../virtual-machines/windows/quotas.md) článku. Výchozí výběr VM vyžaduje 6 jader virtuálního počítače. Změnit na menší velikost virtuálního počítače, jako *v2 Standard DS1* omezuje počet jader na 4.

## <a name="prerequisites"></a>Požadavky

Azure Blockchain Workbench vyžaduje konfiguraci a uplatnění registrace Azure AD. Můžete také provést Azure AD [konfigurace ručně](#azure-ad-configuration) před nasazení nebo spusťte skript po nasazení. Pokud nasazujete Blockchain Workbench, přečtěte si téma [konfigurace služby Azure AD](#azure-ad-configuration) konfiguraci Azure AD ověřit.

> [!IMPORTANT]
> Aplikace Workbench nemusí být nasazená ve stejném tenantovi, které používáte k registraci aplikace Azure AD. Aplikace Workbench musí být nasazeny v tenantovi, kde máte dostatečná oprávnění k nasazení prostředků. Další informace o tenantů Azure AD najdete v tématu [získání tenanta služby Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) a [integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).



## <a name="deploy-blockchain-workbench"></a>Nasadit Blockchain Workbench

Po dokončení nezbytných kroků jste připravení nasadit Blockchain Workbench. Následující oddíly popisují postup nasazení rozhraní framework.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte svůj účet a přepnout na požadovanou tenanta Azure AD, ve které chcete nasadit Azure Blockchain Workbench.
3. V levém podokně vyberte **vytvořit prostředek**. Vyhledejte `Azure Blockchain Workbench` v **Hledat na Marketplace** panelu hledání. 

    ![Panel hledání Marketplace](media/deploy/marketplace-search-bar.png)

4. Vyberte **Azure Blockchain Workbench**.

    ![Výsledky hledání Marketplace](media/deploy/marketplace-search-results.png)

5. Vyberte **Vytvořit**.
6. Dokončení základní nastavení.

    ![Vytvoření Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Předpona prostředků | Krátký jedinečný identifikátor pro vaše nasazení. Tato hodnota se používá jako základ pro vytváření názvů prostředků. |
    | Uživatelské jméno virtuálního počítače | Uživatelské jméno slouží jako správce pro všechny virtuální počítače (VM). |
    | Typ ověřování | Vyberte, pokud chcete použít hesla nebo klíče pro připojení k virtuálním počítačům. |
    | Heslo | Heslo se používá pro připojení k virtuálním počítačům. |
    | SSH | Použít veřejný klíč RSA v jednořádkovém formátu začíná **ssh-rsa** nebo víceřádkovém formátu PEM. Pomocí klíče SSH můžete vygenerovat `ssh-keygen` v Linuxu a OS X nebo PuTTYGen ve Windows. Další informace o klíči SSH, viz [klíče, jak použít SSH s Windows v Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Heslo databáze / potvrďte heslo k databázi | Zadejte heslo pro přístup k databázi vytvořenou jako součást nasazení. |
    | Oblast nasazení | Zadejte, kam chcete nasadit prostředky Blockchain Workbench. Pro nejlepší dostupnost, mělo by to odpovídat **umístění** nastavení. |
    | Předplatné | Zadejte předplatné Azure, které chcete použít pro vaše nasazení. |
    | Skupiny prostředků | Vytvořit novou skupinu prostředků tak, že vyberete **vytvořit nový** a zadejte název skupiny prostředků jedinečný. |
    | Umístění | Zadejte oblast, kterou chcete nasadit rozhraní framework. |

7. Vyberte **OK** dokončete nastavení základní konfigurační oddíl.

8. V **Upřesnit nastavení**, zvolte, pokud chcete vytvořit novou síť blockchain, nebo použijte existující síť testování of-authority blockchain.

    Pro **vytvořit nový**:

    *Vytvořit nový* se vytvoří sada uzlů ethereum během testování of Authority (PoA) v rámci předplatného jeden člen. 

    ![Upřesňující nastavení pro novou síť blockchain](media/deploy/advanced-blockchain-settings-new.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Monitorování | Zvolte, jestli chcete povolit Azure Monitor k monitorování sítě blockchain |
    | Nastavení služby Azure Active Directory | Zvolte **později přidat**.</br>Poznámka: Pokud jste se rozhodli [předem nakonfigurovat služby Azure AD](#azure-ad-configuration) nebo opětovného nasazení, zvolit *přidat nyní*. |
    | Výběr VM | Výběr upřednostňovaného velikosti virtuálního počítače pro vaši síť blockchain. Zvolte menší velikost virtuálního počítače, jako *v2 Standard DS1* na předplatné s omezení s nízkou služeb, jako je Azure – úroveň free. |

    Pro **použít existující**:

    *Použít existující* možnost umožňuje zadat síť blockchain ethereum během testování of-Authority (PoA). Koncové body mají následující požadavky.

    * Koncový bod musí být síť blockchain ethereum během testování of-Authority (PoA).
    * Koncový bod musí být veřejně přístupná přes síť.
    * Síťové blockchainové PoA by měl být povolen mají cenu plynu nastaven na hodnotu nula.

    > [!NOTE]
    > Blockchain Workbench účty nejsou financování. Pokud jsou vyžadovány fondy, transakce se nezdaří.

    ![Upřesňující nastavení pro existující síť blockchain](media/deploy/advanced-blockchain-settings-existing.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Ethereum RPC Endpoint | Zadejte koncový bod existující síť blockchain PoA RPC. Koncový bod začíná na https:// nebo http:// a končí číslem portu. Například `http<s>://<network-url>:<port>`. |
    | Nastavení služby Azure Active Directory | Zvolte **později přidat**.</br>Poznámka: Pokud jste se rozhodli [předem nakonfigurovat služby Azure AD](#azure-ad-configuration) nebo opětovného nasazení, zvolit *přidat nyní*. |
    | Výběr VM | Výběr upřednostňovaného velikosti virtuálního počítače pro vaši síť blockchain. |

9. Vyberte **OK** dokončete Upřesnit nastavení.

10. Kontrola souhrnných informací k ověření, že vaše parametry jsou správné.

    ![Souhrn](media/deploy/blockchain-workbench-summary.png)

11. Vyberte **vytvořit** souhlasit s podmínkami a nasadit vaši aplikaci Azure Blockchain Workbench.

Nasazení může trvat až 90 minut. Na webu Azure portal můžete použít ke sledování průběhu. V nově vytvořenou skupinu, vyberte **nasazení > Přehled** a zjistit stav nasazených artefakty.

> [!IMPORTANT]
> Po nasazení, které potřebujete k dokončení nastavení služby Active Directory. Pokud jste zvolili **přidat později**, je potřeba spustit [skript pro konfiguraci Azure AD](#azure-ad-configuration-script).  Pokud jste zvolili **nyní přidat**, budete muset [konfigurace adresy URL odpovědi](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Adresa URL webové Blockchain Workbench

Po dokončení nasazení Blockchain Workbench, novou skupinu prostředků obsahuje prostředky Blockchain Workbench. Blockchain Workbench služby jsou přístupné prostřednictvím adresu URL webu. Následující kroky ukazují, jak načíst adresu URL webové rozhraní nasazené.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V levém navigačním podokně vyberte **skupiny prostředků**
3. Zvolte název skupiny prostředků, které jste zadali při nasazení Blockchain Workbench.
4. Vyberte **typ** záhlaví sloupce seřadíte seznam podle abecedy podle typu.
5. Existují dva prostředky s typem **služby App Service**. Vyberte prostředek typu **služby App Service** *bez* "– rozhraní api" příponu.

    ![Seznam služeb App](media/deploy/resource-group-list.png)

6. Ve službě App Service **Essentials** tématu, zkopírujte **URL** hodnotu, která představuje adresu URL webové nasazené Blockchain Workbench.

    ![Základy služby aplikací](media/deploy/app-service.png)

Blockchain Workbench umožňuje přidružit vlastní název domény, najdete v článku [konfigurace vlastního názvu domény pro webovou aplikaci ve službě Azure App Service pomocí Traffic Manageru](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Skript pro konfiguraci Azure AD

Azure AD musí být nakonfigurovaný k dokončení nasazení Blockchain Workbench. Provedete konfiguraci použijete skript prostředí PowerShell.

1. V prohlížeči přejděte [adresy URL webového Blockchain Workbench](#blockchain-workbench-web-url).
2. Zobrazí se vám pokyny k nastavení Azure AD pomocí služby Cloud Shell. Zkopírujte tento příkaz a spustit Cloud Shell.

    ![Spusťte skript AAD](media/deploy/launch-aad-script.png)

3. Zvolte tenanta Azure AD, kam jste nasadili Blockchain Workbench.
4. Ve službě Cloud Shell vložit a spusťte příkaz.
5. Po zobrazení výzvy zadejte tenanta Azure AD, kterou chcete použít pro Blockchain Workbench. Bude jím tenanta obsahující uživatele, pro Blockchain Workbench.

    > [!IMPORTANT]
    > Ověřený uživatel vyžaduje oprávnění k vytvoření registrace aplikací Azure AD a udělení oprávnění delegované aplikace v tenantovi. Budete muset požádat správce tenanta ke spuštění skript pro konfiguraci Azure AD nebo vytvořit nového tenanta.

    ![Zadejte tenanta Azure AD](media/deploy/choose-tenant.png)

6. Budete vyzváni k ověření do tenanta služby Azure AD pomocí prohlížeče. Otevření webové adresy URL v prohlížeči, zadejte kód a ověření.

    ![Ověření pomocí kódu](media/deploy/authenticate.png)

7. Skript vypíše několik stavové zprávy. Můžete získat **úspěch** stavovou zprávu, pokud byla úspěšně zřízena tenanta.
8. Přejděte na adresu URL Blockchain Workbench. Zobrazí se výzva k udělení oprávnění ke čtení k adresáři vyjádřit souhlas. Díky tomu Blockchain Workbench přístup k webovým aplikacím uživatelům v tenantovi. Pokud jste správcem tenanta, můžete vyjádřit souhlas pro celou organizaci. Tento příkaz přijme souhlas pro všechny uživatele v tenantovi. Jinak každý uživatel vyzván k zadání souhlasu při prvním použití Blockchain Workbench webové aplikace.
9. Vyberte **přijmout** vyjádřit souhlas.

     ![Vyjadřujete souhlas s čtení profilů uživatelů](media/deploy/graph-permission-consent.png)

10. Po vyjádření souhlasu je možné Blockchain Workbench webové aplikace.

## <a name="azure-ad-configuration"></a>Konfigurace služby Azure AD

Pokud budete chtít ručně nakonfigurovat nebo ověřit nastavení před nasazením služby Azure AD, dokončete všechny kroky v této části. Pokud chcete automaticky nakonfigurovat nastavení služby Azure AD, použijte [skript pro konfiguraci Azure AD](#azure-ad-configuration-script) poté, co nasadíte Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registrace aplikace API kolekce Blockchain Workbench

Blockchain Workbench umožňuje nasazení vyžaduje registrace aplikace Azure AD. Budete potřebovat tenanta služby Azure Active Directory (Azure AD) k registraci aplikace. Můžete používat existujícího tenanta nebo vytvořit nového tenanta. Pokud používáte existujícího tenanta Azure AD, musíte dostatečná oprávnění k registraci aplikací, udělte oprávnění rozhraní Graph API a povolit přístup hosta v rámci tenanta služby Azure AD. Pokud nemáte dostatečná oprávnění v existujícím tenantovi Azure AD vytvořte nového tenanta.


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte svůj účet a přepněte do požadované služby Azure AD tenanta. Klient by měl být správce předplatného tenanta předplatného, ve kterém je nasazená aplikace Workbench a máte dostatečná oprávnění k registraci aplikace.
3. V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **registrace aplikací** > **registrace nové aplikace**.

    ![Registrace aplikace](media/deploy/app-registration.png)

4. Zadejte **název** a **přihlašovací adresa URL** pro aplikaci. Hodnoty zástupných symbolů můžete použít, protože byly změněny hodnoty během nasazení. 

    ![Vytvoření registrace aplikace](media/deploy/app-registration-create.png)

    |Nastavení  | Hodnota  |
    |---------|---------|
    |Název | `Blockchain API` |
    |Typ aplikace |Webová aplikace / webové rozhraní API|
    |Přihlašovací adresa URL | `https://blockchainapi` |

5. Vyberte **vytvořit** registrace aplikace Azure AD.

### <a name="modify-manifest"></a>Upravit manifest

Dále je třeba upravit manifest pro použití aplikačních rolí v rámci Azure AD k určení Blockchain Workbench umožňuje správci.  Další informace o manifestech aplikace naleznete v tématu [manifest aplikace Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Pro aplikace, které jste zaregistrovali, vyberte **Manifest** v podokně podrobností registrované aplikaci.
2. Generování identifikátoru GUID. Můžete generovat identifikátor GUID, pomocí příkazu prostředí PowerShell [identifikátor guid]:: (NewGuid) nebo rutiny New-GUID. Další možností je použití webu generátor identifikátor GUID.
3. Chcete aktualizovat **appRoles** manifestu. V podokně úpravy manifestu vyberte **upravit** a nahraďte `"appRoles": []` pomocí zadaného kódu JSON. Nezapomeňte nahradit hodnotu **id** pole identifikátoru GUID, který jste vygenerovali. 

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
    > Hodnota **správce** , je potřeba identifikovat Blockchain Workbench umožňuje správci.

4. V manifestu, také změnit **Oauth2AllowImplicitFlow** hodnota, která se **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Vyberte **Uložit** manifestu změny uložte.

### <a name="add-graph-api-required-permissions"></a>Přidání oprávnění požadované rozhraní Graph API

Aplikace rozhraní API je potřeba požádat o oprávnění uživatele pro přístup k adresáři. Nastavte následující požadované oprávnění pro aplikaci API:

1. V registraci aplikace API Blockchainu vyberte **Nastavení > požadovaná oprávnění > vyberte rozhraní API > Microsoft Graph**.

    ![Výběr rozhraní API](media/deploy/client-app-select-api.png)

    Klikněte na **Vybrat**.

2. V **povolit přístup z** pod **delegovaná oprávnění**, zvolte **čtení základních profilů všech uživatelů**.

    ![Povolit přístup](media/deploy/client-app-read-perms.png)

    Vyberte **Uložit** vyberte **provádí**.

3. V **požadovaná oprávnění**vyberte **udělit oprávnění** vyberte **Ano** zobrazení výzvy k ověření.

   ![Udělení oprávnění](media/deploy/client-app-grant-permissions.png)

   Udělení oprávnění umožňuje Blockchain Workbench a jak přistupovat k uživatelé v adresáři. K vyhledání a přidání členů do Blockchain Workbench se vyžaduje oprávnění ke čtení.

### <a name="get-application-id"></a>Získání ID aplikace

Informace o aplikaci ID a klienta jsou požadovány pro nasazení. Shromažďování a ukládání informací pro použití během nasazování.

1. Pro aplikace, které jste zaregistrovali, vyberte **nastavení** > **vlastnosti**.
2. V **vlastnosti** podokno, kopírování a úložiště následující hodnoty pro pozdější použití během nasazování.

    ![Vlastnosti aplikace API](media/deploy/app-properties.png)

    | Nastavení pro ukládání  | Použít v nasazení |
    |------------------|-------------------|
    | ID aplikace | Instalace nástroje Azure Active Directory > ID aplikace |

### <a name="get-tenant-domain-name"></a>Získat název domény tenanta

Shromažďování a ukládání název domény tenanta Active Directory, kde jsou registrovány v aplikacích. 

V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Názvy vlastních domén**. Zkopírujte a uložte název domény.

![Název domény](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Nastavení uživatele typu Host

Pokud máte ve vašem tenantovi Azure AD uživatelům typu Host, postupujte podle dalších kroků, aby Blockchain Workbench umožňuje přiřazení uživatelů a správu funguje správně.

1. Přepnout vašeho tenanta Azure AD a vyberte **Azure Active Directory > Nastavení > Spravovat nastavení externí spolupráce**.
2. Nastavte **oprávnění uživatelů typu Host jsou omezená** k **ne**.
    ![Nastavení externí spolupráce](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurace adresy URL odpovědi

Po nasazení Azure Blockchain Workbench, budete muset konfigurovat klientskou aplikaci služby Azure Active Directory (Azure AD) **adresy URL odpovědi** adresa URL webu nasazené Blockchain Workbench.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ověřte, že máte v tenantovi, kde jste zaregistrovali klientskou aplikaci služby Azure AD.
3. V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Registrace aplikací**.
4. Vyberte klientské aplikace Azure AD, které jste zaregistrovali v části požadavků.
5. Vyberte **Nastavení > adresy URL odpovědí**.
6. Zadejte adresu URL hlavní webové aplikaci Azure Blockchain Workbench nasazení, který jste získali v **získat adresu URL Azure Blockchain Workbench webové** oddílu. Adresa URL odpovědi je s předponou `https://`. Například `https://myblockchain2-7v75.azurewebsites.net`.

    ![Adresy URL odpovědí](media/deploy/configure-reply-url.png)

7. Vyberte **Uložit** se aktualizovat registraci klienta.

## <a name="remove-a-deployment"></a>Odebrání nasazení

Pokud nasazení je už nepotřebujete, můžete odebrat nasazení tak, že odstraníte skupinu prostředků Blockchain Workbench.

1. Na webu Azure Portal, přejděte na **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit. 
2. Vyberte **Odstranit skupinu prostředků**. Ověření odstranění proveďte tak, že zadáte název skupiny prostředků a vyberte **odstranit**.

    ![Odstranění skupiny prostředků](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Další postup

V tomto článku s postupy nasadit Azure Blockchain Workbench. Naučíte se vytvářet blockchainové aplikace, pokračujte na další článek.

> [!div class="nextstepaction"]
> [Vytvoření blockchainové aplikace v Azure Blockchain Workbench](create-app.md)
