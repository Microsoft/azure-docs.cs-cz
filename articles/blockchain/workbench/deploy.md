---
title: Nasadit Azure Blockchain Workbench
description: Jak nasadit Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 3ee169e4139f5fc9cd4208c53c4997d50521fed7
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242407"
---
# <a name="deploy-azure-blockchain-workbench"></a>Nasadit Azure Blockchain Workbench

Azure Blockchain Workbench se nasadí pomocí šablony řešení na webu Azure Marketplace. Šablona zjednodušuje nasazení součásti potřebné k vytvoření blockchainové aplikace. Po nasazení Blockchain Workbench poskytuje přístup ke klientské aplikace k vytváření a správě uživatelů a blockchainové aplikace.

Další informace o komponentách Blockchain Workbench najdete v tématu [architektury Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení

Blockchain Workbench umožňuje nasadit blockchain účetní kniha společně se sadou relevantní služby Azure, které jsou nejčastěji používají k vytvoření blockchainové aplikace. Nasazení Blockchain Workbench výsledkem v rámci skupiny prostředků ve vašem předplatném Azure zřídí následující služby Azure.

* Téma 1 event gridu
* Namespace 1 Service Bus
* 1 application Insights
* 1 SQL Database (Standard S0)
* 2 app Services (Standard)
* 2 trezory klíčů azure
* 2 účtů služby azure Storage (Standard LRS)
* 2 škálovací sady virtuálních počítačů (pro program pro ověření a pracovní uzly)
* 2 virtuální sítě (včetně nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě a veřejnou IP adresu pro každou virtuální síť)
* Volitelné: Azure Monitor

Tady je příklad nasazení vytvořit v **myblockchain** skupinu prostředků.

![Příklad nasazení](media/deploy/example-deployment.png)

Náklady na Blockchain Workbench není agregovaný a nákladů na podpůrné služby Azure. Informace o cenách pro Azure services je možné vypočítat pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/).

Azure Blockchain Workbench potřeba splnit několik předpokladů předcházející nasazení. Požadavky zahrnují konfiguraci a uplatnění registrace Azure AD.

### <a name="blockchain-workbench-api-app-registration"></a>Registrace aplikace API kolekce Blockchain Workbench

Blockchain Workbench umožňuje nasazení vyžaduje registrace aplikace Azure AD. Budete potřebovat tenanta služby Azure Active Directory (Azure AD) k registraci aplikace. Můžete používat existujícího tenanta nebo vytvořit nového tenanta. Pokud používáte existujícího tenanta Azure AD, musíte dostatečná oprávnění k registraci aplikací a udělení oprávnění rozhraní Graph API v rámci tenanta služby Azure AD. Pokud nemáte dostatečná oprávnění v existujícím tenantovi Azure AD vytvořte nového tenanta. 

> [!IMPORTANT]
> Aplikace Workbench nemusí být nasazená ve stejném tenantovi, které používáte k registraci aplikace Azure AD. Aplikace Workbench musí být nasazeny v tenantovi, kde máte dostatečná oprávnění k nasazení prostředků. Další informace o tenantů Azure AD najdete v tématu [získání tenanta služby Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) a [integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte svůj účet v pravém horním rohu a přepnout na požadovanou Azure AD tenanta. Klient by měl být správce předplatného tenanta předplatného, ve kterém je nasazená aplikace Workbench a máte dostatečná oprávnění k registraci aplikace.
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

### <a name="modify-application-manifest"></a>Upravit manifest aplikace

Dále je třeba upravit manifest aplikace pomocí aplikačních rolí v rámci Azure AD můžete určit Blockchain Workbench umožňuje správci.  Další informace o manifestech aplikace naleznete v tématu [manifest aplikace Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Pro aplikace, které jste zaregistrovali, vyberte **Manifest** v podokně podrobností registrované aplikaci.
2. Generování identifikátoru GUID. Můžete vygenerovat identifikátor GUID, pomocí příkazu prostředí PowerShell [identifikátor guid]:: NewGuid () nebo rutiny New-GUID. Další možností je použití webu generátor identifikátor GUID.
3. Chcete aktualizovat **appRoles** manifestu. V podokně úpravy manifestu vyberte **upravit** a nahraďte `"appRoles": []` pomocí zadaného kódu JSON. Nezapomeňte nahradit hodnotu **id** pole identifikátoru GUID, který jste vygenerovali. 

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

    ![Upravit manifest](media/deploy/edit-manifest.png)

    > [!IMPORTANT]
    > Hodnota **správce** , je potřeba identifikovat Blockchain Workbench umožňuje správci.

4.  Klikněte na tlačítko **Uložit** se uložit změny manifestu aplikace.

### <a name="add-graph-api-required-permissions"></a>Přidání oprávnění požadované rozhraní Graph API

Aplikace rozhraní API je potřeba požádat o oprávnění uživatele pro přístup k adresáři. Nastavte následující požadované oprávnění pro aplikaci API:

1. V registraci aplikace API Blockchainu vyberte **Nastavení > požadovaná oprávnění > vyberte rozhraní API > Microsoft Graph**.

    ![Výběr rozhraní API](media/deploy/client-app-select-api.png)

    Klikněte na **Vybrat**.

2. V **povolit přístup z** pod **oprávnění aplikace**, zvolte **čtení úplných profilů všech uživatelů**.

    ![Povolit přístup](media/deploy/client-app-read-perms.png)

    Klikněte na tlačítko **vyberte** klikněte **provádí**.

3. V **požadovaná oprávnění**vyberte **udělit oprávnění** vyberte **Ano** zobrazení výzvy k ověření.

   ![Udělení oprávnění](media/deploy/client-app-grant-permissions.png)

   Udělení oprávnění umožňuje Blockchain Workbench a jak přistupovat k uživatelé v adresáři. K vyhledání a přidání členů do Blockchain Workbench se vyžaduje oprávnění ke čtení.

### <a name="add-graph-api-key-to-application"></a>Přidat klíč rozhraní Graph API k aplikaci

Blockchain Workbench umožňuje uživatelům interakci s blockchainové aplikace používá Azure AD jako hlavní identita systému pro správu. Aby Blockchain Workbench umožňuje přístup ke službě Azure AD a načítání informací o uživatelích, jako jsou jména a e-mailů budete muset přidat přístupový klíč. Blockchain Workbench používá klíč k ověření ve službě Azure AD.

1. Pro aplikace, které jste zaregistrovali, vyberte **nastavení** v podokně podrobností registrované aplikaci.
2. Vyberte **Klíče**.
3. Přidejte nový klíč zadáním klíče **popis** a zvolíte **vyprší platnost** hodnoty duration. 

    ![Vytvořit klíč](media/deploy/app-key-create.png)

    |Nastavení  | Hodnota  |
    |---------|---------|
    | Popis | `Service` |
    | Platnost vyprší | Zvolte doba vypršení platnosti |

4. Vyberte **Uložit**. 
5. Hodnotu klíče zkopírovat a uložit pro pozdější. Budete potřebovat pro nasazení.

    > [!IMPORTANT]
    >  Pokud je neuložíte klíč pro nasazení, musíte vygenerovat nový klíč. Hodnota klíče nelze načíst později z portálu.

### <a name="get-application-id"></a>Získání ID aplikace

Informace o aplikaci ID a klienta jsou požadovány pro nasazení. Shromažďování a ukládání informací pro použití během nasazování.

1. Pro aplikace, které jste zaregistrovali, vyberte **nastavení** > **vlastnosti**.
2.  V **vlastnosti** podokno, kopírování a úložiště následující hodnoty pro pozdější použití během nasazování.

    ![Vlastnosti aplikace API](media/deploy/app-properties.png)

    | Nastavení pro ukládání  | Použít v nasazení |
    |------------------|-------------------|
    | ID aplikace | Instalace nástroje Azure Active Directory > ID aplikace |

### <a name="get-tenant-domain-name"></a>Získat název domény tenanta

Shromažďování a ukládání název domény tenanta Active Directory, kde jsou registrovány v aplikacích. 

V levém navigačním podokně vyberte službu **Azure Active Directory**. Vyberte **Názvy vlastních domén**. Zkopírujte a uložte název domény.

![Název domény](media/deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Nasadit Blockchain Workbench

Po dokončení nezbytných kroků jste připravení nasadit Blockchain Workbench. Následující oddíly popisují postup nasazení rozhraní framework.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  V pravém rohu a přepnout na požadovanou Azure AD tenanta, ve které chcete nasadit Azure Blockchain Workbench vyberte svůj účet.
3.  V levém podokně vyberte **vytvořit prostředek**. Vyhledejte `Azure Blockchain Workbench` v **Hledat na Marketplace** panelu hledání. 

    ![Panel hledání Marketplace](media/deploy/marketplace-search-bar.png)

4.  Vyberte **Azure Blockchain Workbench**.

    ![Výsledky hledání Marketplace](media/deploy/marketplace-search-results.png)

4.  Vyberte **Vytvořit**.
5.  Dokončení základní nastavení.

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

6.  Vyberte **OK** dokončete nastavení základní konfigurační oddíl.

7.  Dokončení **instalační program služby Azure Active Directory**.

    ![Azure AD – nastavení](media/deploy/blockchain-workbench-settings-aad.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Název domény | Pomocí služby Azure AD tenant soustředěné [název domény tenantu Get](#get-tenant-domain-name) část předpoklady v tématu. |
    | ID aplikace | Pomocí ID aplikace z registrace aplikace klienta Blockchain shromážděných v [získání ID aplikace](#get-application-id) část předpoklady v tématu. |
    | Klíč aplikace | Použijte klíč aplikace z registrace aplikace klienta Blockchain shromážděných v [klíč přidat rozhraní Graph API k aplikaci](#add-graph-api-key-to-application) část předpoklady v tématu. |


8.  Klikněte na tlačítko **OK** dokončete konfiguračního oddílu parametry Azure AD.

9.  V **nastavení sítě a výkonu**, zvolte, pokud chcete vytvořit novou síť blockchain, nebo použijte existující síť testování of-authority blockchain.

    Pro **vytvořit nový**:

    *Vytvořit nový* se vytvoří sada uzlů ethereum během testování of Authority (PoA) v rámci předplatného jeden člen. 

    ![Nastavení sítě a výkonu](media/deploy/blockchain-workbench-settings-network-new.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Počet uzlů blockchain | Zvolte počet Etherea PoA uzly program pro ověření nasazení ve vaší síti. |
    | Výkon úložiště | Vyberte upřednostňovaný výkon úložiště virtuálního počítače pro vaši síť blockchain. |
    | Velikost virtuálního počítače | Výběr upřednostňovaného velikosti virtuálního počítače pro vaši síť blockchain. |

    Pro **použít existující**:

    *Použít existující* možnost umožňuje zadat síť blockchain ethereum během testování of-Authority (PoA). Koncové body mají následující požadavky.

    * Koncový bod musí být síť blockchain ethereum během testování of-Authority (PoA).
    * Koncový bod musí být veřejně přístupná přes síť.
    * Síťové blockchainové PoA by měl být povolen mají cenu plynu nastaven na hodnotu nula (Poznámka: nejsou financování Blockchain Workbench účty. Pokud jsou vyžadovány fondy, transakce selže).

    ![Nastavení sítě a výkonu](media/deploy/blockchain-workbench-settings-network-existing.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Koncový bod Etherea RPC | Zadejte koncový bod existující síť blockchain PoA RPC. Koncový bod začíná http:// a končí číslem portu. Například `http://contoso-chain.onmicrosoft.com:8545`. |
    | Výkon úložiště | Vyberte upřednostňovaný výkon úložiště virtuálního počítače pro vaši síť blockchain. |
    | Velikost virtuálního počítače | Výběr upřednostňovaného velikosti virtuálního počítače pro vaši síť blockchain. |

10. Vyberte **OK** dokončete nastavení sítě a výkonu.

11. Dokončení **Azure Monitor** nastavení.

    ![Azure Monitor](media/deploy/blockchain-workbench-settings-oms.png)

    | Nastavení | Popis  |
    |---------|--------------|
    | Monitorování | Zvolte, jestli chcete povolit Azure Monitor k monitorování sítě blockchain |
    | Připojte se k existující instanci Log Analytics | Zvolte, zda chcete použít existující instanci Log Analytics, nebo vytvořte novou. Pokud používáte existující instanci, zadejte ID pracovního prostoru a primární klíč. |

12. Klikněte na tlačítko **OK** k dokončení části Azure Monitor.

13. Kontrola souhrnných informací k ověření, že vaše parametry jsou správné.

    ![Souhrn](media/deploy/blockchain-workbench-summary.png)

14. Vyberte **vytvořit** souhlasit s podmínkami a nasadit vaši aplikaci Azure Blockchain Workbench.

Nasazení může trvat až 90 minut. Na webu Azure portal můžete použít ke sledování průběhu. V nově vytvořenou skupinu, vyberte **nasazení > Přehled** a zjistit stav nasazených artefakty.

## <a name="blockchain-workbench-web-url"></a>Adresa URL webové Blockchain Workbench

Po dokončení nasazení Blockchain Workbench, novou skupinu prostředků obsahuje prostředky Blockchain Workbench. Blockchain Workbench služby jsou přístupné prostřednictvím adresu URL webu. Následující kroky ukazují, jak načíst adresu URL webové rozhraní nasazené.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V levém navigačním podokně vyberte **skupiny prostředků**
3. Zvolte název skupiny prostředků, které jste zadali při nasazení Blockchain Workbench.
4. Klikněte na tlačítko **typ** záhlaví sloupce seřadíte seznam podle abecedy podle typu.
5. Existují dva prostředky s typem **služby App Service**. Vyberte prostředek typu **služby App Service** *bez* "– rozhraní api" příponu.

    ![Seznam služeb App](media/deploy/resource-group-list.png)

6.  Ve službě App Service **Essentials** tématu, zkopírujte **URL** hodnotu, která představuje adresu URL webové nasazené Blockchain Workbench.

    ![Základy služby aplikací](media/deploy/app-service.png)

Blockchain Workbench umožňuje přidružit vlastní název domény, najdete v článku [konfigurace vlastního názvu domény pro webovou aplikaci ve službě Azure App Service pomocí Traffic Manageru](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Konfigurace adresy URL odpovědi

Po nasazení Azure Blockchain Workbench, dalším krokem je Ujistěte se, že klientská aplikace služby Azure Active Directory (Azure AD) je zaregistrován na správné **adresy URL odpovědi** adresa URL webu nasazené Blockchain Workbench.

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
