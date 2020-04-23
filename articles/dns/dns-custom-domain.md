---
title: Integrace Azure DNS s prostředky Azure – Azure DNS
description: V tomto článku se dozvíte, jak pomocí Azure DNS spolu poskytovat DNS pro vaše prostředky Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 56a7680de3127da06341ac03252a9ab0cff9da7c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024944"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Použití Azure DNS k poskytování nastavení vlastní domény pro službu Azure

Azure DNS poskytuje DNS pro vlastní doménu pro všechny vaše prostředky Azure, které podporují vlastní domény nebo které mají plně kvalifikovaný název domény (Plně kvalifikovaný název domény). Příkladem je, že máte webovou aplikaci Azure a chcete, aby\.k ní uživatelé přistupovali pomocí contoso.com nebo www contoso.com jako vyvedenní přístupu k síti. Tento článek vás provede konfigurací služby Azure pomocí Azure DNS pro použití vlastních domén.

## <a name="prerequisites"></a>Požadované součásti

Abyste mohli azure DNS používat pro vlastní doménu, musíte nejdřív delegovat svou doménu na Azure DNS. Pokyny k konfiguraci názvových serverů pro delegování najdete na stránce [Delegování domény na Azure DNS.](./dns-delegate-domain-azure-dns.md) Jakmile je vaše doména delegována do zóny Azure DNS, můžete nakonfigurovat potřebné záznamy DNS.

Můžete nakonfigurovat ješitnost nebo vlastní doménu pro [aplikace Azure Function Apps](#azure-function-app), veřejné IP [adresy](#public-ip-address), App [Service (Web Apps),](#app-service-web-apps) [úložiště objektů blob](#blob-storage)a [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Azure Function App

Chcete-li nakonfigurovat vlastní doménu pro aplikace funkcí Azure, vytvoří se záznam CNAME a konfigurace samotné aplikace funkce.
 
Přejděte do **aplikace Function App** a vyberte aplikaci funkcí. Klikněte na **Funkce platformy** a v části **Síť** klikněte na **Vlastní domény**.

![okno aplikace funkce](./media/dns-custom-domain/functionapp.png)

Všimněte si aktuální adresy URL v okně **Vlastní domény,** tato adresa se používá jako alias pro vytvořený záznam DNS.

![okno vlastní domény](./media/dns-custom-domain/functionshostname.png)

Přejděte do zóny DNS a klepněte na tlačítko **+ Sada záznamů**. Vyplňte následující informace v okně **Přidat sadu záznamů** a klepnutím na **OK** ji vytvořte.

|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | myfunctionapp        | Tato hodnota spolu s popiskem názvu domény je hlavní název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použijte záznam CNAME, který používá alias.        |
|Hodnota TTL     | 1        | 1 se používá po dobu 1 hodiny        |
|Jednotka hodnoty TTL     | Hodiny        | Hodiny se používají jako měření času         |
|Alias     | adatumfunction.azurewebsites.net        | Název DNS, pro který vytváříte alias, je v tomto příkladu adatumfunction.azurewebsites.net název DNS, který je ve výchozím nastavení k dispozici aplikaci funkce.        |

Přejděte zpět do aplikace funkce, klikněte na **funkce platformy**a v části **Síť** klikněte na Vlastní domény a potom v části **Vlastní názvy hostitelů** klikněte na + Přidat **název_hostitele**. **Custom domains**

V okně **Přidat název_hostitele** zadejte záznam CNAME do textového pole **název hostitele** a klepněte na tlačítko **Ověřit**. Pokud je záznam nalezen, zobrazí se tlačítko **Přidat název hostitele.** Kliknutím na **Přidat název hostitele** přidejte alias.

![aplikace funkce přidat název hostitele blade](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Veřejná IP adresa

Chcete-li nakonfigurovat vlastní doménu pro služby, které používají prostředek veřejné IP adresy, jako je aplikační brána, vyrovnávání zatížení, cloudová služba, virtuální počítače Správce prostředků a klasické virtuální počítače, použije se záznam A.

Přejděte na **síťovou** > **veřejnou IP adresu**, vyberte veřejný prostředek IP a klepněte na tlačítko **Konfigurace**. Notate ip adresu zobrazenou.

![veřejná ip čepel](./media/dns-custom-domain/publicip.png)

Přejděte do zóny DNS a klepněte na tlačítko **+ Sada záznamů**. Vyplňte následující informace v okně **Přidat sadu záznamů** a klepnutím na **OK** ji vytvořte.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | mywebserver        | Tato hodnota spolu s popiskem názvu domény je hlavní název domény pro vlastní název domény.        |
|Typ     | A        | Jako prostředek je IP adresa.        |
|Hodnota TTL     | 1        | 1 se používá po dobu 1 hodiny        |
|Jednotka hodnoty TTL     | Hodiny        | Hodiny se používají jako měření času         |
|IP adresa     | `<your ip address>`       | Veřejná IP adresa.|

![vytvoření záznamu A](./media/dns-custom-domain/arecord.png)

Po vytvoření záznamu A, `nslookup` spusťte ověřit záznam řeší.

![veřejné vyhledávání IP dns](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Služba aplikace (webové aplikace)

Následující kroky vás provedou konfigurací vlastní domény pro webovou aplikaci služby app service.

Přejděte na **službu App Service** a vyberte prostředek, který konfigurujete vlastní název domény, a klepněte na **položku Vlastní domény**.

Všimněte si aktuální adresy URL v okně **Vlastní domény,** tato adresa se používá jako alias pro vytvořený záznam DNS.

![okno vlastní domény](./media/dns-custom-domain/url.png)

Přejděte do zóny DNS a klepněte na tlačítko **+ Sada záznamů**. Vyplňte následující informace v okně **Přidat sadu záznamů** a klepnutím na **OK** ji vytvořte.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | mywebserver        | Tato hodnota spolu s popiskem názvu domény je hlavní název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použijte záznam CNAME, který používá alias. Pokud by prostředek používal adresu IP, byl by použit záznam A.        |
|Hodnota TTL     | 1        | 1 se používá po dobu 1 hodiny        |
|Jednotka hodnoty TTL     | Hodiny        | Hodiny se používají jako měření času         |
|Alias     | webserver.azurewebsites.net        | Název DNS, pro který vytváříte alias, je v tomto příkladu webserver.azurewebsites.net název DNS, který je ve výchozím nastavení k dispozici webové aplikaci.        |


![vytvoření záznamu CNAME](./media/dns-custom-domain/createcnamerecord.png)

Přejděte zpět na službu aplikace, která je nakonfigurovaná pro vlastní název domény. Klepněte na **položku Vlastní domény**a potom na **položku Názvy hostitelů**. Chcete-li přidat vytvořený záznam CNAME, klepněte na tlačítko **+ Přidat název_hostitele**.

![Obrázek 1](./media/dns-custom-domain/figure1.png)

Po dokončení procesu spusťte **nslookup,** abyste ověřili překlad názvů, který funguje.

![Obrázek 1](./media/dns-custom-domain/finalnslookup.png)

Další informace o mapování vlastní domény na službu App Service najdete v části [Mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Informace o migraci aktivního názvu DNS najdete [v tématu Migrace aktivního názvu DNS do služby Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Pokud potřebujete zakoupit vlastní doménu, navštivte [stránku Koupit vlastní název domény pro Azure Web Apps,](../app-service/manage-custom-dns-buy-domain.md) kde se dozvíte víc o doménách služby App Service.

## <a name="blob-storage"></a>Blob Storage

Následující kroky vás provedou konfigurací záznamu CNAME pro účet úložiště objektů blob pomocí metody asverify. Tato metoda zajišťuje, že neexistuje žádné prostoje.

Přejděte **na** > **Účty úložiště**, vyberte účet úložiště a klikněte na Vlastní **doména**. Notate plně kvalifikovaný název v rámci kroku 2, tato hodnota se používá k vytvoření prvního záznamu CNAME

![vlastní doména úložiště objektů blob](./media/dns-custom-domain/blobcustomdomain.png)

Přejděte do zóny DNS a klepněte na tlačítko **+ Sada záznamů**. Vyplňte následující informace v okně **Přidat sadu záznamů** a klepnutím na **OK** ji vytvořte.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | asverify.mystorageaccount        | Tato hodnota spolu s popiskem názvu domény je hlavní název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použijte záznam CNAME, který používá alias.        |
|Hodnota TTL     | 1        | 1 se používá po dobu 1 hodiny        |
|Jednotka hodnoty TTL     | Hodiny        | Hodiny se používají jako měření času         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Název DNS, pro který vytváříte alias, je v tomto příkladu asverify.adatumfunctiona9ed.blob.core.windows.net název DNS, který je ve výchozím nastavení k dispozici pro účet úložiště.        |

Přejděte zpět na účet úložiště **tak,** > že kliknete na**Účty úložiště**, vyberte účet úložiště a klikněte na **Vlastní doména**. Zadejte alias, který jste vytvořili bez předpony asverify, do textového pole, zaškrtněte **políčko Použít nepřímé ověření CNAME**a klepněte na **uložit**. Po dokončení tohoto kroku se vraťte do zóny DNS a vytvořte záznam CNAME bez předpony asverify.  Po tomto okamžiku můžete bezpečně odstranit záznam CNAME s předponou cdnverify.

![vlastní doména úložiště objektů blob](./media/dns-custom-domain/indirectvalidate.png)

Ověření rozlišení DNS spuštěním`nslookup`

Další informace o mapování vlastní domény na koncový bod úložiště objektů blob najdete na stránce [Konfigurace vlastního názvu domény pro koncový bod úložiště objektů BLOB](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Následující kroky vás provedou konfigurací záznamu CNAME pro koncový bod CDN pomocí metody cdnverify. Tato metoda zajišťuje, že neexistuje žádné prostoje.

Přejděte na**profily CDN** **sítě** > , vyberte svůj profil CDN.

Vyberte koncový bod, se kterým pracujete, a klepněte na tlačítko **+ Vlastní doména**. Všimněte **si, že název hostitele koncového bodu,** protože tato hodnota je záznam, na který odkazuje záznam CNAME.

![Vlastní doména CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Přejděte do zóny DNS a klepněte na tlačítko **+ Sada záznamů**. Vyplňte následující informace v okně **Přidat sadu záznamů** a klepnutím na **OK** ji vytvořte.

|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | cdnverify.mycdnendpoint        | Tato hodnota spolu s popiskem názvu domény je hlavní název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použijte záznam CNAME, který používá alias.        |
|Hodnota TTL     | 1        | 1 se používá po dobu 1 hodiny        |
|Jednotka hodnoty TTL     | Hodiny        | Hodiny se používají jako měření času         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Název DNS, pro který vytváříte alias, je v tomto příkladu cdnverify.adatumcdnendpoint.azureedge.net název DNS, který je ve výchozím nastavení k dispozici pro účet úložiště.        |

Přejděte zpět do koncového bodu CDN klepnutím na**položku Profily CDN** **sítě** > a vyberte svůj profil CDN. Klepněte na **+ Vlastní doména** a zadejte alias záznamu CNAME bez předpony cdnverify a klepněte na tlačítko **Přidat**.

Po dokončení tohoto kroku se vraťte do zóny DNS a vytvořte záznam CNAME bez předpony cdnverify.  Po tomto okamžiku můžete bezpečně odstranit záznam CNAME s předponou cdnverify. Další informace o síti CDN a o konfiguraci vlastní domény bez přechodového kroku registrace najdete v části [Mapa obsahu AZURE CDN do vlastní domény](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nakonfigurovat reverzní DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).
