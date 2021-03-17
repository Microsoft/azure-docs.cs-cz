---
title: Nasazení a konfigurace Azure Firewall Premium Preview
description: Přečtěte si, jak nasadit a nakonfigurovat Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721782"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Nasazení a konfigurace Azure Firewall Premium Preview

> [!IMPORTANT]
> Azure Firewall Premium je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview je brána firewall nové generace s funkcemi, které jsou potřeba pro vysoce citlivá a regulovaná prostředí. Zahrnuje následující funkce:

- **Kontrola TLS** – dešifruje odchozí přenosy, zpracovává data a pak data zašifruje a pošle je do cíle.
- **Zprostředkovatelů identity** – systém zjišťování a prevence vniknutí k síti (zprostředkovatelů identity) umožňuje sledovat síťové aktivity pro škodlivou aktivitu, protokolovat informace o této aktivitě, nahlásit je a volitelně se pokusit o její blokování.
- **Filtrování adresy URL** – rozšiřuje schopnost filtrování plně kvalifikovaného názvu domény Azure firewall, aby bylo možné zvážit celou adresu URL. Například `www.contoso.com/a/c` místo `www.contoso.com` .
- **Webové kategorie** – správci můžou povolit nebo odepřít přístup uživatelům k kategoriím webu, jako jsou weby hazardních her, weby sociálních médií a další.

Další informace najdete v tématu [Azure firewall Premium – funkce](premium-features.md).

Použijete šablonu k nasazení testovacího prostředí, které má centrální virtuální síť (10.0.0.0/16) se třemi podsítěmi:
- podsíť pracovního procesu (10.0.10.0/24)
- podsíť Azure bastionu (10.0.20.0/24)
- podsíť brány firewall (10.0.100.0/24)

V tomto testovacím prostředí se pro jednoduchost používá jedna centrální virtuální síť. Pro produkční účely je [topologie rozbočovače a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) se virtuální sítěem partnerských uzlů běžnější.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Centrální topologie VNet":::

Virtuální počítač pracovního procesu je klient, který odesílá požadavky HTTP/S přes bránu firewall.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="deploy-the-infrastructure"></a>Nasazení infrastruktury

Šablona nasadí kompletní testovací prostředí pro Azure Firewall Premium s povoleným zprostředkovatelů identity, kontrolou TLS, filtrováním adres URL a webovými kategoriemi:

- Nová zásada Azure Firewall Premium a brány firewall s předdefinovaným nastavením umožňující snadné ověření základních schopností (zprostředkovatelů identity, kontrola TLS, filtrování adres URL a webové kategorie)
- nasadí všechny závislosti včetně Key Vault a spravované identity. V produkčním prostředí se tyto prostředky už můžou vytvořit a ve stejné šabloně není potřeba.
- vygeneruje kořenovou certifikační autoritu, která je svým jménem a nasadí ji na vygenerovanou Key Vault
-  vygeneruje odvozenou zprostředkující certifikační autoritu a nasadí ji na testovací virtuální počítač se systémem Windows (WorkerVM).
- je nasazený i hostitel bastionu (BastionHost), který se dá použít pro připojení k testovacímu počítači Windows (WorkerVM).



[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>Testování brány firewall

Nyní můžete testovat zprostředkovatelů identity, kontrolu TLS, webové filtrování a webové kategorie.

### <a name="add-firewall-diagnostics-settings"></a>Přidat nastavení diagnostiky brány firewall

Chcete-li shromáždit protokoly brány firewall, je nutné přidat nastavení diagnostiky pro shromažďování protokolů brány firewall.

1. Vyberte **DemoFirewall** a v části **monitorování** vyberte **nastavení diagnostiky**.
2. Vyberte **Přidat nastavení diagnostiky**.
3. Jako **název nastavení diagnostiky** zadejte *FW-Diagnostics*.
4. V části **protokol** vyberte **AzureFirewallApplicationRule** a **AzureFirewallNetworkRule**.
5. V části **Podrobnosti o cíli** vyberte **Odeslat do Log Analytics pracovní prostor**.
6. Vyberte **Uložit**.

### <a name="idps-tests"></a>ZPROSTŘEDKOVATELŮ identity testy

K otestování zprostředkovatelů identity budete muset nasadit vlastní interní webový server s příslušným certifikátem serveru. Další informace o požadavcích na certifikát Azure Firewall Premium Preview najdete v tématu [Azure firewall certifikátů verze Premium Preview](premium-certificates.md).

Můžete použít `curl` k řízení různých hlaviček protokolu HTTP a simulaci škodlivého provozu.

#### <a name="to-test-idps-for-http-traffic"></a>Testování zprostředkovatelů identity pro přenosy HTTP:

1. Na virtuálním počítači s WorkerVM otevřete okno příkazového řádku správce.
2. Do příkazového řádku zadejte následující příkaz:

   `curl -A "BlackSun" <your web server address>`
3. Zobrazí se odpověď webového serveru.
4. Pokud chcete najít výstrahu podobnou této zprávě, přečtěte si síťové pravidlo brány firewall protokoly Azure Portal.

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Zpráva výstrahy":::

   > [!NOTE]
   > Může trvat nějakou dobu, než se data začnou zobrazovat v protokolech. Dejte mu aspoň 20 minut, než se umožní, aby protokoly začaly zobrazovat data.
5. Přidejte pravidlo signatury pro signaturu 2008983:

   1. Vyberte **DemoFirewallPolicy** a v části **Nastavení** vyberte **zprostředkovatelů identity (Preview)**.
   1. Vyberte kartu **pravidla podpisu** .
   1. V části **ID podpisu** zadejte do pole otevřít text *2008983*.
   1. V části **režim** vyberte **Odepřít**.
   1. Vyberte **Uložit**.
   1. Než budete pokračovat, počkejte na dokončení nasazení.



6. V WorkerVM `curl` znovu spusťte příkaz:

   `curl -A "BlackSun" <your web server address>`

   Vzhledem k tomu, že je žádost HTTP teď blokovaná bránou firewall, po vypršení časového limitu připojení se zobrazí následující výstup:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. V Azure Portal otevřete protokoly monitorování a vyhledejte zprávu pro blokovaný požadavek.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>Testování zprostředkovatelů identity pro přenosy HTTPS

Opakujte tyto testy oblé místo HTTP pomocí protokolu HTTPS. Například:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Měli byste vidět stejné výsledky jako u testů HTTP.

### <a name="tls-inspection-with-url-filtering"></a>Kontrola TLS s filtrováním adres URL

Pomocí následujících kroků otestujete kontrolu TLS pomocí filtrování adres URL.

1. Upravte pravidla použití zásad brány firewall a přidejte do kolekce pravidel nové pravidlo s názvem `AllowURL` `AllowWeb` . Nakonfigurujte cílovou adresu URL `www.nytimes.com/section/world` , zdrojovou IP adresu **\* *_, cílový typ _* URL (Preview)**, vyberte možnost **Kontrola TLS (Preview)** a protokoly **http, https**.

3. Až se nasazení dokončí, otevřete prohlížeč na WorkerVM a pokračujte `https://www.nytimes.com/section/world` a ověřte, jestli se v prohlížeči zobrazí odpověď HTML podle očekávání.
4. V Azure Portal můžete zobrazit celou adresu URL v protokolech monitorování pro pravidlo aplikace:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Zpráva s výstrahou ukazující adresu URL":::

Některé stránky HTML můžou být neúplné, protože odkazují na jiné adresy URL, které jsou odepřeny. K vyřešení tohoto problému je možné provést následující přístup:

- Pokud stránka HTML obsahuje odkazy na jiné domény, můžete tyto domény přidat do nového pravidla aplikace s povoleným přístupem k těmto plně kvalifikovaným názvům domény.
- Pokud stránka HTML obsahuje odkazy na dílčí adresy URL, můžete upravit pravidlo a přidat hvězdičku k adrese URL. Příklad: `targetURLs=www.nytimes.com/section/world*`

   Alternativně můžete k pravidlu přidat novou adresu URL. Například: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Testování kategorií webu

Pojďme vytvořit pravidlo aplikace, které umožní přístup k sportovních webům.
1. Na portálu otevřete skupinu prostředků a vyberte **DemoFirewallPolicy**.
2. Vyberte **pravidla aplikace** a pak **přidejte kolekci pravidel**.
3. Jako **název** zadejte *GeneralWeb*, **priority** *103*, **skupina kolekcí pravidel** vybrat **DefaultApplicationRuleCollectionGroup**.
4. V části **pravidla** pro typ **názvu** *AllowSports*, **zdroj** *\** , **protokol** *http, https*, vybrat **kontrolu TLS**, **typ cíle** vybrat *webové kategorie (Preview)*, **cíl** vybrat *Sport*.
5. Vyberte **Přidat**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Sportovní webová kategorie":::
6. Po dokončení nasazení přejděte na  **WorkerVM** a otevřete webový prohlížeč a přejděte na adresu `https://www.nfl.com` .

   Měla by se zobrazit webová stránka fotbalové a v protokolu pro aplikace se zobrazí informace o tom, že se shodovalo pravidlo **webové kategorie: sportovní** a žádost byla povolena.

## <a name="next-steps"></a>Další kroky

- [Azure Firewall Premium Preview v Azure Portal](premium-portal.md)