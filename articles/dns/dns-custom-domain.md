---
title: Integrace Azure DNS s prostředky Azure – Azure DNS
description: V tomto článku se dozvíte, jak pomocí Azure DNS společně zadat DNS pro prostředky Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 4d8af5815e544698ab833001e5ce6d0f4a30a264
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487394"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Použití Azure DNS k poskytnutí vlastní nastavení domény pro službu Azure

Azure DNS poskytuje DNS pro vlastní doménu pro všechny prostředky Azure, které podporují vlastní domény nebo mají plně kvalifikovaný název domény (FQDN). Příkladem může být webová aplikace Azure a chcete, aby k nim měli přístup uživatelé pomocí contoso.com nebo webové \. contoso.com jako plně kvalifikovaný název domény. Tento článek vás provede konfigurací služby Azure pomocí Azure DNS pro použití vlastních domén.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné použít Azure DNS pro vlastní doménu, musíte nejprve delegovat doménu na Azure DNS. Pokyny ke konfiguraci názvových serverů pro delegování najdete v tématu [delegování domény a Azure DNS](./dns-delegate-domain-azure-dns.md) . Jakmile je vaše doména delegovaná do vaší zóny Azure DNS, budete moct nakonfigurovat potřebné záznamy DNS.

Můžete nakonfigurovat individuální nebo vlastní doménu pro [aplikace funkcí Azure](#azure-function-app), [veřejné IP adresy](#public-ip-address), [App Service (Web Apps)](#app-service-web-apps), [úložiště objektů BLOB](#blob-storage)a [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Function App Azure

Pokud chcete nakonfigurovat vlastní doménu pro aplikace Azure Functions, vytvoří se záznam CNAME a taky se nakonfiguruje v samotné aplikaci Function App.
 
Přejděte na **Function App** a vyberte svou aplikaci Function App. Klikněte na **funkce platformy** a v části **sítě** klikněte na **vlastní domény**.

![okno aplikace Function App](./media/dns-custom-domain/functionapp.png)

Všimněte si aktuální adresy URL v okně **vlastní domény** . Tato adresa se používá jako alias pro vytvořený záznam DNS.

![okno vlastní doména](./media/dns-custom-domain/functionshostname.png)

Přejděte do zóny DNS a klikněte na **+ Sada záznamů**. V okně **Přidat sadu záznamů** zadejte následující informace a kliknutím na tlačítko **OK** ji vytvořte.

|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | myfunctionapp        | Tato hodnota spolu s popiskem názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použití záznamu CNAME používá alias.        |
|TTL     | 1        | 1 se používá po dobu 1 hodiny.        |
|Jednotka TTL     | Hodiny        | Hodiny se používají jako časová měření.         |
|Alias     | adatumfunction.azurewebsites.net        | Název DNS, pro který vytváříte alias, v tomto příkladu se jedná o název DNS adatumfunction.azurewebsites.net, který je ve výchozím nastavení zadaný pro aplikaci Function App.        |

Přejděte zpátky do aplikace Function App, klikněte na **funkce platformy** a v části **sítě** klikněte na **vlastní domény** a potom v části **vlastní názvy hostitelů** klikněte na **+ Přidat název hostitele**.

V okně **Přidat název hostitele** zadejte záznam CNAME do textového pole **název hostitele** a klikněte na **ověřit**. Pokud se záznam najde, zobrazí se tlačítko **Přidat název hostitele** . Kliknutím na **Přidat název hostitele** přidejte alias.

![okno aplikace Function app přidat název hostitele](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Veřejná IP adresa

Pokud chcete nakonfigurovat vlastní doménu pro služby, které používají prostředky veřejné IP adresy, například Application Gateway, Load Balancer, cloudové služby, Správce prostředků virtuální počítače a klasické virtuální počítače, použije se záznam A.

Přejděte na **síť**  >  **Veřejná IP adresa**, vyberte prostředek veřejné IP adresy a klikněte na **Konfigurace**. Setatete zobrazenou IP adresu.

![okno veřejné IP adresy](./media/dns-custom-domain/publicip.png)

Přejděte do zóny DNS a klikněte na **+ Sada záznamů**. V okně **Přidat sadu záznamů** zadejte následující informace a kliknutím na tlačítko **OK** ji vytvořte.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | MyWebServer        | Tato hodnota spolu s popiskem názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | A        | Použijte záznam A jako prostředek je IP adresa.        |
|TTL     | 1        | 1 se používá po dobu 1 hodiny.        |
|Jednotka TTL     | Hodiny        | Hodiny se používají jako časová měření.         |
|IP adresa     | `<your ip address>`       | Veřejná IP adresa.|

![Vytvoření záznamu A](./media/dns-custom-domain/arecord.png)

Po vytvoření záznamu A spusťte příkaz `nslookup` k ověření, že se záznam vyřeší.

![vyhledání DNS pro veřejnou IP adresu](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Následující kroky vás provedou konfigurací vlastní domény pro webovou aplikaci App Service.

Přejděte na **App Service** a vyberte prostředek, pro který konfigurujete vlastní název domény, a klikněte na **vlastní domény**.

Všimněte si aktuální adresy URL v okně **vlastní domény** . Tato adresa se používá jako alias pro vytvořený záznam DNS.

![okno vlastní domény](./media/dns-custom-domain/url.png)

Přejděte do zóny DNS a klikněte na **+ Sada záznamů**. V okně **Přidat sadu záznamů** zadejte následující informace a kliknutím na tlačítko **OK** ji vytvořte.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | MyWebServer        | Tato hodnota spolu s popiskem názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použití záznamu CNAME používá alias. Pokud prostředek použil IP adresu, bude použit záznam A.        |
|TTL     | 1        | 1 se používá po dobu 1 hodiny.        |
|Jednotka TTL     | Hodiny        | Hodiny se používají jako časová měření.         |
|Alias     | webserver.azurewebsites.net        | Název DNS, pro který vytváříte alias, v tomto příkladu se jedná o název DNS webserver.azurewebsites.net, který je ve výchozím nastavení zadaný pro webovou aplikaci.        |


![Vytvoření záznamu CNAME](./media/dns-custom-domain/createcnamerecord.png)

Přejděte zpátky do služby App Service, která je nakonfigurovaná pro vlastní název domény. Klikněte na **vlastní domény** a potom klikněte na **názvy hostitelů**. Chcete-li přidat záznam CNAME, který jste vytvořili, klikněte na **+ Přidat název hostitele**.

![Snímek obrazovky, který zvýrazní tlačítko + Přidat název hostitele.](./media/dns-custom-domain/figure1.png)

Po dokončení procesu spusťte příkaz **nslookup** a ověřte, jestli funguje překlad názvů.

![Obrázek 1](./media/dns-custom-domain/finalnslookup.png)

Další informace o mapování vlastní domény na App Service najdete v [Web Apps mapování existujícího vlastního názvu DNS na Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Informace o tom, jak migrovat aktivní název DNS, najdete v tématu [migrace aktivního názvu DNS na Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Pokud potřebujete koupit vlastní doménu, přečtěte si téma [koupit si vlastní název domény pro Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md) , kde najdete další informace o App Service doménách.

## <a name="blob-storage"></a>Blob Storage

Následující kroky vás provedou konfigurací záznamu CNAME pro účet úložiště objektů BLOB pomocí metody asverify. Tato metoda zajistí, že nedochází k výpadkům.

Přejděte na **úložiště**  >  **účty** úložiště, vyberte svůj účet úložiště a klikněte na **vlastní doména**. V části Krok 2 se zobrazí plně kvalifikovaný název domény. Tato hodnota se používá k vytvoření prvního záznamu CNAME.

![vlastní doména BLOB Storage](./media/dns-custom-domain/blobcustomdomain.png)

Přejděte do zóny DNS a klikněte na **+ Sada záznamů**. V okně **Přidat sadu záznamů** zadejte následující informace a kliknutím na tlačítko **OK** ji vytvořte.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | asverify. mystorageaccount        | Tato hodnota spolu s popiskem názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použití záznamu CNAME používá alias.        |
|TTL     | 1        | 1 se používá po dobu 1 hodiny.        |
|Jednotka TTL     | Hodiny        | Hodiny se používají jako časová měření.         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Název DNS, pro který vytváříte alias, v tomto příkladu se jedná o název DNS asverify.adatumfunctiona9ed.blob.core.windows.net, který se ve výchozím nastavení zadal pro účet úložiště.        |

Přejděte zpátky na svůj účet úložiště kliknutím na **úložiště**  >  **účty** úložiště, vyberte svůj účet úložiště a klikněte na **vlastní doména**. Do textového pole zadejte alias, který jste vytvořili bez předpony asverify, zaškrtněte políčko **použít nepřímé ověřování CNAME** a klikněte na **Uložit**. Až se tento krok dokončí, vraťte se do zóny DNS a vytvořte záznam CNAME bez předpony asverify.  Po tomto okamžiku můžete bezpečně odstranit záznam CNAME s předponou cdnverify.

![Snímek obrazovky zobrazující stránku vlastní doména](./media/dns-custom-domain/indirectvalidate.png)

Ověřte překlad DNS spuštěním `nslookup`

Další informace o mapování vlastní domény na koncový bod služby Blob Storage najdete v téma [Konfigurace vlastního názvu domény pro koncový bod služby Blob Storage](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json) .

## <a name="azure-cdn"></a>Azure CDN

Následující kroky vás provedou konfigurací záznamu CNAME pro koncový bod CDN pomocí metody cdnverify. Tato metoda zajistí, že nedochází k výpadkům.

Přejděte do **sítě**  >  **CDN profily**, vyberte svůj profil CDN.

Vyberte koncový bod, se kterým pracujete, a klikněte na **+ vlastní doména**. Poznamenejte si **název hostitele koncového bodu** , protože tato hodnota je záznam, na který odkazuje záznam CNAME.

![Vlastní doména CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Přejděte do zóny DNS a klikněte na **+ Sada záznamů**. V okně **Přidat sadu záznamů** zadejte následující informace a kliknutím na tlačítko **OK** ji vytvořte.

|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | cdnverify. mycdnendpoint        | Tato hodnota spolu s popiskem názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použití záznamu CNAME používá alias.        |
|TTL     | 1        | 1 se používá po dobu 1 hodiny.        |
|Jednotka TTL     | Hodiny        | Hodiny se používají jako časová měření.         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Název DNS, pro který vytváříte alias, v tomto příkladu se jedná o název DNS cdnverify.adatumcdnendpoint.azureedge.net, který se ve výchozím nastavení zadal pro účet úložiště.        |

Přejděte zpátky na koncový bod CDN kliknutím na **sítě**  >  **profily CDN** a vyberte svůj profil CDN. Klikněte na **+ vlastní doména** a zadejte alias záznamu CNAME bez předpony cdnverify a klikněte na **Přidat**.

Až se tento krok dokončí, vraťte se do zóny DNS a vytvořte záznam CNAME bez předpony cdnverify.  Po tomto okamžiku můžete bezpečně odstranit záznam CNAME s předponou cdnverify. Další informace o CDN a o tom, jak nakonfigurovat vlastní doménu bez kroku zprostředkující registrace, najdete v tématu [mapování Azure CDN obsahu do vlastní domény](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Další kroky

Naučte se [Konfigurovat reverzní DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).
