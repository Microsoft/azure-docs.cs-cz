---
title: 'Kurz: mapování stávajícího vlastního názvu DNS'
description: Naučte se, jak přidat existující vlastní název domény DNS (individuální Domain) do webové aplikace, back-endu mobilní aplikace nebo aplikace API v Azure App Service.
keywords: App Service, Azure App Service, mapování domény, název domény, existující doména, název hostitele, doména individuální
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ca1308c969227336bfb4970f7c5c77b9f2e0cc22
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216526"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Kurz: mapování stávajícího vlastního názvu DNS na Azure App Service

V tomto kurzu se dozvíte, jak namapovat existující <abbr title="Název domény, který jste si koupili od doménového registrátora, jako je například GoDaddy, nebo subdoménou vaší koupené domény.">vlastní název domény DNS</abbr> na <abbr title="Služba založená na protokolu HTTP pro hostování webových aplikací, rozhraní REST API a mobilní back-endové aplikace.">Azure App Service</abbr>.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Mapování subdomény pomocí <abbr title="Záznam kanonického názvu DNS mapuje jeden název domény na jiný.">Záznam CNAME</abbr>.
> * Mapování kořenové domény pomocí <abbr title="Záznam adresy ve službě DNS mapuje název hostitele na IP adresu.">Záznam A</abbr>.
> * Mapování domény se zástupnými znaky pomocí záznamu CNAME.
> * Přesměrujte výchozí adresu URL na vlastní adresář.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

* [Vytvořit plán služby App Service](./index.yml) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.
* Ujistěte se, že můžete upravovat záznamy DNS pro vlastní doménu. Pokud ještě nemáte vlastní doménu, můžete si [koupit doménu App Service](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Co potřebuji k úpravě záznamů DNS?</summary>
        Vyžaduje přístup k registru DNS vašeho poskytovatele domény, například GoDaddy. Abyste například mohli přidat záznamy DNS pro <code>contoso.com</code> a <code>www.contoso.com</code>, musíte mít možnost konfigurovat nastavení DNS pro kořenovou doménu <code>contoso.com</code>.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. Příprava aplikace

K namapování vlastního názvu DNS na aplikaci aplikace <abbr title="Určuje umístění, velikost a funkce webové serverové farmy, která je hostitelem vaší aplikace.">Plán služby App Service</abbr> musí se jednat o placenou úroveň (ne <abbr title="Azure App Service vrstva, ve které vaše aplikace běží na stejných virtuálních počítačích jako jiné aplikace, včetně aplikací jiných zákazníků. Tato úroveň je určená pro vývoj a testování.">**Zdarma (F1)**</abbr>). Další informace najdete v tématu [Přehled plánu Azure App Service](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete [Azure Portal](https://portal.azure.com)a přihlaste se pomocí svého účtu Azure.

#### <a name="select-the-app-in-the-azure-portal"></a>Výběr aplikace v Azure Portal

1. Vyhledejte a vyberte **App Services**.

   ![Snímek obrazovky, který zobrazuje výběr App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na stránce **App Services** vyberte název aplikace Azure.

   ![Snímek obrazovky znázorňující navigaci na portálu do aplikace Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Zobrazí se stránka pro správu aplikace App Service.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

1. V levém podokně stránky aplikace přejděte do části **Nastavení** a vyberte **horizontální navýšení kapacity (App Service plán)**.

   ![Snímek obrazovky s nabídkou pro horizontální navýšení kapacity (App Service plán)](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Ujistěte se, že aplikace není ve vrstvě **F1** . Vlastní DNS se nepodporuje ve vrstvě **F1** .

   ![Snímek obrazovky, který ukazuje Doporučené cenové úrovně.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Pokud plán App Service není ve vrstvě **F1** , zavřete stránku **horizontálního navýšení kapacity** a přejděte na [3. Získá ID ověření domény](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

1. Vyberte některou z placených úrovní (**D1**, **B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti **zobrazíte výběrem možnosti Zobrazit další možnosti**.

1. Vyberte **Použít**.

   ![Snímek obrazovky, který ukazuje kontrolu cenové úrovně.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Až se zobrazí následující oznámení, operace škálování je dokončená.

   ![Snímek obrazovky, který ukazuje potvrzení operace škálování.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. získání ID ověření domény

Pokud chcete do aplikace přidat vlastní doménu, musíte ověřit vlastnictví domény tím, že přidáte ID ověřování jako záznam TXT pro vašeho poskytovatele domény. 

1. V levém podokně stránky aplikace vyberte **vlastní domény**. 
1. Zkopírujte ID do pole **ID ověření vlastní domény** na stránce **vlastní domény** pro další krok.

    ![Snímek obrazovky zobrazující ID v poli ID ověření vlastní domény](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Proč to potřebuji?</summary>
        Přidání identifikátorů ověřování domény do vlastní domény může zabránit tomu, aby se dangling záznamy DNS a usnadnily převzetí subdomény. U vlastních domén, které jste dříve nakonfigurovali bez tohoto ID ověření, byste je měli chránit před stejným rizikem přidáním ID ověření do záznamu DNS. Další informace o této společné hrozbě s vysokou závažností najdete v tématu <a href="/azure/security/fundamentals/subdomain-takeover">převzetí subdomény</a>.
    </details>
    
<a name="info"></a>

3. **(Pouze záznam)** Mapování <abbr title="Záznam adresy ve službě DNS mapuje název hostitele na IP adresu.">Záznam A</abbr>potřebujete externí IP adresu aplikace. Na stránce **vlastní domény** Zkopírujte hodnotu **IP adresa**.

   ![Snímek obrazovky, který zobrazuje navigaci na portálu do aplikace Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. vytvoření záznamů DNS

1. Přihlaste se k webu vašeho poskytovatele domény.

    <details>
        <summary>Můžu ze svého poskytovatele domény spravovat DNS pomocí Azure?</summary>
        Pokud chcete, můžete pomocí Azure DNS spravovat záznamy DNS pro vaši doménu a nakonfigurovat vlastní název DNS pro Azure App Service. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns">kurz: Hostování vaší domény v Azure DNS></a>.
    </details>

1. Vyhledejte stránku pro správu záznamů DNS. 

    <details>
        <summary>Návody stránku najít?</summary>
        <p>Každý poskytovatel domén má vlastní rozhraní pro správu záznamů DNS, takže se obraťte na dokumentaci poskytovatele. Hledejte oblasti webu označené jako <strong>Domain Name</strong> (Název domény), <strong>DNS</strong> nebo <strong>Name Server Management</strong> (Správa názvových serverů).</p>
        <p>Často můžete najít stránku záznamy DNS zobrazením informací o účtu a pak vyhledat odkaz, jako je <strong>moje doména</strong>. Přejít na tuto stránku a vyhledat odkaz s názvem, jako je <strong>soubor zóny</strong>, <strong>záznamy DNS</strong>nebo <strong>Pokročilá konfigurace</strong>.</p>
    </details>

   Následující snímek obrazovky obsahuje příklad stránky záznamů DNS:

   ![Snímek obrazovky, který zobrazuje ukázkovou stránku záznamů DNS.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Vyberte **Přidat** nebo odpovídající pomůcku k vytvoření záznamu. 

1. Vyberte typ záznamu, který chcete vytvořit, a postupujte podle pokynů. Můžete použít buď <abbr title="Záznam kanonického názvu v DNS mapuje jeden název domény (alias) na jiný (kanonický název).">Záznam CNAME</abbr> nebo <abbr title="Záznam adresy ve službě DNS mapuje název hostitele na IP adresu.">Záznam A</abbr> Mapování vlastního názvu DNS na App Service. 

    <details>
        <summary>Který záznam mám zvolit?</summary>
        <div>
            <ul>
            <li>Chcete-li namapovat kořenovou doménu (například <code>contoso.com</code> ), použijte záznam A. Nepoužívejte záznam CNAME pro kořenový záznam (informace najdete v tématu <a href="https://en.wikipedia.org/wiki/CNAME_record">Wikipedii entry</a>).</li>
            <li>Chcete-li namapovat subdoménu (například <code>www.contoso.com</code> ), použijte záznam CNAME.</li>
            <li>Subdoménu můžete namapovat na IP adresu aplikace přímo pomocí záznamu A, ale je možné, <a href="https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">že se IP adresa změní</a>. Záznam CNAME místo toho mapuje na název hostitele aplikace, což je méně náchylné ke změně.</li>
            <li>Chcete-li namapovat <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">zástupnou doménu</a> (například <code>*.contoso.com</code> ), použijte záznam CNAME.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

V případě subdomény `www` , jako v `www.contoso.com` , vytvořte dva záznamy podle následující tabulky:

| Typ záznamu | Hostitel | Hodnota | Komentáře |
| - | - | - |
| CNAME | `<subdomain>` (například `www` ) | `<app-name>.azurewebsites.net` | Samotné mapování domény. |
| TXT | `asuid.<subdomain>` (například `asuid.www` ) | [ID ověření, které jste získali dříve](#3-get-a-domain-verification-id) | App Service přistupuje k `asuid.<subdomain>` záznamu TXT a ověří vlastnictví vlastní domény. |

![Snímek obrazovky zobrazující navigaci na portálu do aplikace Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Pro kořenovou doménu jako `contoso.com` Vytvořte dva záznamy podle následující tabulky:

| Typ záznamu | Hostitel | Hodnota | Komentáře |
| - | - | - |
| A | `@` | IP adresa z části [Zkopírování IP adresy aplikace](#3-get-a-domain-verification-id) | Samotné mapování domény ( `@` obvykle představuje kořenovou doménu). |
| TXT | `asuid` | [ID ověření, které jste získali dříve](#3-get-a-domain-verification-id) | App Service přistupuje k `asuid.<subdomain>` záznamu TXT a ověří vlastnictví vlastní domény. Pro kořenovou doménu použijte `asuid` . |

![Snímek obrazovky zobrazující stránku záznamů DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Co když chci namapovat subdoménu na záznam A?</summary>
Chcete-li namapovat subdoménu podobný záznamu a `www.contoso.com` místo doporučeného záznamu CNAME, měl by váš záznam a záznam TXT vypadat jako v následující tabulce:

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">Tabulka 3</caption>
<thead>
<tr>
<th>Typ záznamu</th>
<th>Hostitel</th>
<th>Hodnota</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td><code>&lt;subdomain&gt;</code> (například <code>www</code> )</td>
<td>IP adresa z části <a href="#info" data-linktype="self-bookmark">Zkopírování IP adresy aplikace</a></td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (například <code>asuid.www</code> )</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">ID ověření, které jste získali dříve</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Zástupný znak (CNAME)](#tab/wildcard)

Pro zástupný název, jako `*` v `*.contoso.com` , vytvořte dva záznamy podle následující tabulky:

| Typ záznamu | Hostitel | Hodnota | Komentáře |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Samotné mapování domény. |
| TXT | `asuid` | [ID ověření, které jste získali dříve](#3-get-a-domain-verification-id) | App Service přistupuje k `asuid` záznamu TXT a ověří vlastnictví vlastní domény. |

![Snímek obrazovky, který zobrazuje navigaci na aplikaci Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Moje změny se po opuštění stránky vymažou.</summary>
<p>U určitých poskytovatelů, jako je například GoDaddy, se změny záznamů DNS neprojeví, dokud nevyberete samostatný odkaz <strong>Save Changes</strong> (Uložit změny).</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. povolení mapování ve vaší aplikaci

1. V levém podokně stránky aplikace v Azure Portal vyberte **vlastní domény**.

    ![Snímek obrazovky, který zobrazuje nabídku vlastní domény.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který zobrazuje položku pro přidání názvu hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Zadejte plně kvalifikovaný název domény, pro který jste přidali záznam CNAME, například `www.contoso.com`.

1. Vyberte **Ověřit**. Zobrazí se stránka **Přidat vlastní doménu** .

1. Ujistěte se, že **typ záznamu názvu hostitele** je nastavený na **CNAME (webová \. example.com nebo libovolná subdoména)**. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který zobrazuje tlačítko Přidat vlastní doménu](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Může trvat nějakou dobu, než se nová vlastní doména projeví na stránce **vlastní domény** aplikace. Aktualizujte prohlížeč, aby se data aktualizovala.

    ![Snímek obrazovky, který ukazuje přidání záznamu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Co je popisek <strong>nezabezpečeného</strong> upozornění?</summary>
        Označení upozornění pro vaši vlastní doménu znamená, že ještě není vázané na certifikát TLS/SSL. Jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény obdrží chybu nebo varování v závislosti na prohlížeči. Pokud chcete přidat vazbu TLS, přečtěte si téma <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service</a>.
    </details>

    Pokud jste nějaký krok neudělali nebo jste ho provedli dříve, zobrazí se v dolní části stránky chyba ověřování.

    ![Snímek obrazovky, který zobrazuje chybu ověřování.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Zadejte plně kvalifikovaný název domény, pro který jste nakonfigurovali záznam A, například `contoso.com`. 

1. Vyberte **Ověřit**. Zobrazí se stránka **Přidat vlastní doménu** .

1. Ujistěte se, že **Typ záznamu názvu hostitele** je nastavený na **Záznam A (www.example.com)**. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který ukazuje přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Může trvat nějakou dobu, než se nová vlastní doména projeví na stránce **vlastní domény** aplikace. Aktualizujte prohlížeč, aby se data aktualizovala.

    ![Snímek obrazovky, který ukazuje přidání záznamu A](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Co je popisek <strong>nezabezpečeného</strong> upozornění?</summary>
        Označení upozornění pro vaši vlastní doménu znamená, že ještě není vázané na certifikát TLS/SSL. Jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény obdrží chybu nebo varování v závislosti na prohlížeči. Pokud chcete přidat vazbu TLS, přečtěte si téma <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service</a>.
    </details>
    
    Pokud jste nějaký krok neudělali nebo jste ho provedli dříve, zobrazí se v dolní části stránky chyba ověřování.
    
    ![Snímek obrazovky znázorňující chybu ověřování](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Zástupný znak (CNAME)](#tab/wildcard)

3. Zadejte plně kvalifikovaný název domény, který odpovídá zástupné doméně. Například `*.contoso.com` můžete použít `sub1.contoso.com` ,, `sub2.contoso.com` `*.contoso.com` nebo jakýkoli jiný řetězec, který odpovídá vzoru zástupného znaku. Pak vyberte **ověřit**.

    Aktivuje se tlačítko **Přidat vlastní doménu** .

1. Ujistěte se, že **typ záznamu názvu hostitele** je nastavený na **záznam CNAME (webová \. example.com nebo libovolná subdoména)**. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který zobrazuje přidání názvu DNS do aplikace.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Může trvat nějakou dobu, než se nová vlastní doména projeví na stránce **vlastní domény** aplikace. Aktualizujte prohlížeč, aby se data aktualizovala.

    <details>
        <summary>Co je popisek <strong>nezabezpečeného</strong> upozornění?</summary>
        Označení upozornění pro vaši vlastní doménu znamená, že ještě není vázané na certifikát TLS/SSL. Jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény obdrží chybu nebo varování v závislosti na prohlížeči. Pokud chcete přidat vazbu TLS, přečtěte si téma <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. test v prohlížeči

Přejděte na názvy DNS, které jste nakonfigurovali dříve.

![Snímek obrazovky, který ukazuje navigaci na aplikaci Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Zobrazila se chyba HTTP 404 (Nenalezeno).</summary>
<ul>
<li>V konfiguraci vlastní domény chybí záznam A nebo záznam CNAME.</li>
<li>Prohlížeč uložil do mezipaměti starou IP adresu vaší domény. Vymažte mezipaměť a znovu otestujte překlad DNS. Na počítači s Windows můžete mezipaměť vymazat příkazem <code>ipconfig /flushdns</code>.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Migrace aktivní domény

Pokud chcete do služby App Service migrovat živý web a jeho název domény DNS bez výpadku, přečtěte si článek o [migraci aktivního názvu DNS do služby Azure App Service](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Přesměrování do vlastního adresáře

<details>
<summary>Potřebuji to?</summary>
<p>Záleží na vaší aplikaci. Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Některé webové architektury ale&#39;v kořenovém adresáři nezačínají. Například <a href="https://laravel.com/">Laravel</a> začíná v podadresáři <code>public</code>. Pokud chcete v <code>contoso.com</code> příkladu DNS pokračovat, taková aplikace je přístupná v <code>http://contoso.com/public</code> , ale <code>http://contoso.com</code> místo toho chcete směrovat do <code>public</code> adresáře. </p>
</details>

I když se jedná o běžný scénář, neznamená to, že nezahrnuje vlastní mapování domény, ale přizpůsobuje se tím virtuální adresář v rámci vaší aplikace.

1. V levém podokně stránky vaší webové aplikace vyberte **nastavení aplikace** .

1. V dolní části stránky kořenový virtuální adresář `/` odkazuje ve výchozím nastavení na adresář `site\wwwroot`, což je kořenový adresář kódu vaší aplikace. Změňte ho tak, aby místo toho odkazoval například na adresář `site\wwwroot\public`, a uložte provedené změny.

    ![Snímek obrazovky, který ukazuje přizpůsobení virtuálního adresáře.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Po dokončení operace ověřte, že v prohlížeči přejdete na kořenovou cestu vaší aplikace (například `http://contoso.com` nebo `http://<app-name>.azurewebsites.net` ).

<hr/> 

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/)můžete automatizovat správu vlastních domén pomocí skriptů.

#### <a name="azure-cli"></a>Azure CLI

Následující příkaz přidá do aplikace App Service nakonfigurovaný vlastní název DNS.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Další informace najdete v tématu [Mapování vlastní domény na webovou aplikaci](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příkaz přidá do aplikace App Service nakonfigurovaný vlastní název DNS.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Další informace najdete v tématu [Přiřazení vlastní domény k webové aplikaci](scripts/powershell-configure-custom-domain.md).

<hr/> 

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit vazby vlastního certifikátu TLS/SSL k webové aplikaci.

> [!div class="nextstepaction"]
> [Zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md)
