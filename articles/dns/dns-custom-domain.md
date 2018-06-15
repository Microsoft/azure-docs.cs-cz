---
title: Azure DNS integrovat vašich prostředků Azure | Microsoft Docs
description: Naučte se používat Azure DNS podél zajistit DNS pro vašich prostředků Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/19/2018
ms.author: kumud
ms.openlocfilehash: cbc769cd7356b3057fd2aae295071b04d2e40d91
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2018
ms.locfileid: "27994378"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Zadejte nastavení vlastní domény pro službu Azure pomocí Azure DNS

Azure DNS poskytuje DNS pro vlastní doménu pro některé z vašich prostředků Azure, podporu vlastní domény nebo který mít platný plně kvalifikovaný název domény (FQDN). Příkladem je máte webové aplikace Azure a mají vaši uživatelé k němu přístup, a to buď pomocí contoso.com nebo www.contoso.com jako plně kvalifikovaný název domény. Tento článek vás provede procesem konfigurace služby Azure s Azure DNS pro používání vlastní domény.

## <a name="prerequisites"></a>Požadavky

Abyste mohli používat Azure DNS pro vaši vlastní doménu, musíte nejprve delegování domény do Azure DNS. Navštivte [delegování domény do Azure DNS](./dns-delegate-domain-azure-dns.md) pokyny ke konfiguraci serverů název pro delegování. Jakmile doménu se deleguje na zónu Azure DNS, budete moci nakonfigurovat záznamy DNS, které jsou potřeba.

Můžete nakonfigurovat jednoduché nebo vlastní doména pro [Azure funkce aplikace](#azure-function-app), [veřejné IP adresy](#public-ip-address), [služby App Service (webové aplikace)](#app-service-web-apps), [Blob storage](#blob-storage), a [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Aplikace Azure – funkce

Pokud chcete konfigurovat vlastní doménu pro Azure funkce aplikace, se vytvoří záznam CNAME a také konfigurace na funkce aplikace.
 
Přejděte na **jiných** > **aplikaci funkce** a vyberte svou aplikaci funkce. Klikněte na tlačítko **funkce** a v části **sítě** klikněte na tlačítko **vlastní domény**.

![okno aplikace – funkce](./media/dns-custom-domain/functionapp.png)

Všimněte si aktuální adresa url, na **vlastní domény** okně tato adresa se používá jako alias pro vytvoření záznamu DNS.

![okno vlastní doménu.](./media/dns-custom-domain/functionshostname.png)

Přejděte do zóny DNS a klikněte na tlačítko **+ sady záznamů**. Zadejte následující informace na **přidat sadu záznamů** a klikněte na **OK** k jeho vytvoření.

|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | myfunctionapp        | Tato hodnota společně s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použití záznam CNAME používá alias.        |
|TTL     | 1        | 1 se používá pro 1 hodina        |
|Hodnota TTL jednotky     | Hodiny        | Hodiny se používají jako měření času         |
|Alias     | adatumfunction.azurewebsites.net        | Název DNS vytváříte alias, v tomto příkladu je název DNS adatumfunction.azurewebsites.net dostupné ve výchozím nastavení do aplikaci funkce.        |

Přejděte zpět do aplikace pro funkce, klikněte na **funkce**a v části **sítě** klikněte na tlačítko **vlastní domény**, pak v části **názvy hostitelů**klikněte na tlačítko **+ přidat název hostitele**.

Na **přidat název hostitele** okno, zadejte záznam CNAME v **hostname** textové pole a klikněte na tlačítko **ověřením**. Pokud záznam bylo možné najít, **přidat název hostitele** se zobrazí tlačítko. Klikněte na tlačítko **přidat název hostitele** přidat alias.

![funkce aplikace přidat okno název hostitele](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Veřejná IP adresa

Konfigurovat vlastní doménu pro služby, které používají veřejných IP adres prostředků jako aplikační brána, nástroj pro vyrovnávání zatížení, Cloudová služba, virtuální počítače správce prostředků a zaznamenejte klasické virtuální počítače, záznam CNAME použít.

Přejděte na **sítě** > **veřejnou IP adresu**, vyberte prostředek veřejné IP adresy a klikněte na **konfigurace**. Zaznamenání, zobrazí IP adresu.

![okno veřejnou IP adresu](./media/dns-custom-domain/publicip.png)

Přejděte do zóny DNS a klikněte na tlačítko **+ sady záznamů**. Zadejte následující informace na **přidat sadu záznamů** a klikněte na **OK** k jeho vytvoření.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | mywebserver        | Tato hodnota společně s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | A        | Použijte záznam prostředku je IP adresa.        |
|TTL     | 1        | 1 se používá pro 1 hodina        |
|Hodnota TTL jednotky     | Hodiny        | Hodiny se používají jako měření času         |
|IP adresa     | <your ip address>       | Veřejnou IP adresu.|

![Vytvoření záznamu a.](./media/dns-custom-domain/arecord.png)

Jakmile je vytvořen záznam A, spustit `nslookup` ověření záznamů vyřeší.

![vyhledávání dns veřejné IP adresy](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Služby App Service (webové aplikace)

Následující postup vás provede konfigurace vlastní domény pro webové aplikace app service.

Přejděte na **Web a mobilní** > **služby App Service** a vyberte prostředek, který konfigurujete vlastní název domény a klikněte na tlačítko **vlastní domény**.

Všimněte si aktuální adresa url, na **vlastní domény** okně tato adresa se používá jako alias pro vytvoření záznamu DNS.

![Okno vlastních domén](./media/dns-custom-domain/url.png)

Přejděte do zóny DNS a klikněte na tlačítko **+ sady záznamů**. Zadejte následující informace na **přidat sadu záznamů** a klikněte na **OK** k jeho vytvoření.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | mywebserver        | Tato hodnota společně s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použití záznam CNAME používá alias. Pokud prostředek používá IP adresu, se použije záznam.        |
|TTL     | 1        | 1 se používá pro 1 hodina        |
|Hodnota TTL jednotky     | Hodiny        | Hodiny se používají jako měření času         |
|Alias     | webserver.azurewebsites.net        | Název DNS vytváříte alias, v tomto příkladu je název DNS webserver.azurewebsites.net dostupné ve výchozím nastavení do webové aplikace.        |


![Vytvořte záznam CNAME](./media/dns-custom-domain/createcnamerecord.png)

Přejděte zpět do služby app service, který je nakonfigurován pro vlastní název domény. Klikněte na tlačítko **vlastní domény**, pak klikněte na tlačítko **názvy hostitelů**. Chcete-li přidat záznam CNAME, který jste vytvořili, klikněte na tlačítko **+ přidat název hostitele**.

![Obrázek 1](./media/dns-custom-domain/figure1.png)

Po dokončení procesu spuštění **nslookup** ověření překlad názvů funguje.

![Obrázek 1](./media/dns-custom-domain/finalnslookup.png)

Další informace o mapování vlastní doménu do služby App Service, navštivte [mapovat existující vlastní název DNS pro službu Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Pokud potřebujete koupit vlastní doménu, navštivte [koupit vlastní název domény pro Azure Web Apps](../app-service/custom-dns-web-site-buydomains-web-app.md) Další informace o doménách služby App Service.

## <a name="blob-storage"></a>Blob Storage

Následující postup vás provede konfiguraci záznam CNAME pro účet úložiště blob pomocí metody asverify. Tato metoda zajišťuje, že nedojde k žádnému výpadku.

Přejděte na **úložiště** > **účty úložiště**, vyberte svůj účet úložiště a klikněte na **vlastní domény**. Zaznamenání, plně kvalifikovaný název domény v kroku 2, tato hodnota se používá k vytvoření první záznam CNAME

![objekt BLOB úložiště vlastní domény](./media/dns-custom-domain/blobcustomdomain.png)

Přejděte do zóny DNS a klikněte na tlačítko **+ sady záznamů**. Zadejte následující informace na **přidat sadu záznamů** a klikněte na **OK** k jeho vytvoření.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | asverify.mystorageaccount        | Tato hodnota společně s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použití záznam CNAME používá alias.        |
|TTL     | 1        | 1 se používá pro 1 hodina        |
|Hodnota TTL jednotky     | Hodiny        | Hodiny se používají jako měření času         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Název DNS vytváříte alias, v tomto příkladu je název DNS asverify.adatumfunctiona9ed.blob.core.windows.net dostupné ve výchozím nastavení k účtu úložiště.        |

Přejděte zpět na váš účet úložiště kliknutím **úložiště** > **účty úložiště**, vyberte svůj účet úložiště a klikněte na **vlastní domény**. Zadejte alias, který jste vytvořili bez předpony asverify v textovém poli, zkontrolujte ** použít nepřímé ověření CNAME a klikněte na tlačítko **Uložit**. Po dokončení tohoto kroku se vraťte do zóny DNS a vytvořte záznam CNAME bez předpony asverify.  Po tomto bodě se bezpečně odstranit záznam CNAME s předponou cdnverify.

![objekt BLOB úložiště vlastní domény](./media/dns-custom-domain/indirectvalidate.png)

Ověřte překlad DNS spuštěním`nslookup`

Další informace o mapování vlastní doménu na koncový bod úložiště objektů blob najdete [konfigurace vlastního názvu doménu pro koncový bod služby Blob storage](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Následující postup vás provede konfiguraci záznam CNAME pro koncový bod CDN pomocí metody cdnverify. Tato metoda zajišťuje, že nedojde k žádnému výpadku.

Přejděte na **sítě** > **profilů CDN**vyberte váš profil CDN a klikněte na tlačítko **koncové body** pod **Obecné**.

Vyberte koncový bod se práce a klikněte na tlačítko **+ vlastní domény**. Poznámka: **hostitele koncového bodu** jako tato hodnota je záznam, který záznam CNAME odkazuje na.

![Vlastní doména CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Přejděte do zóny DNS a klikněte na tlačítko **+ sady záznamů**. Zadejte následující informace na **přidat sadu záznamů** a klikněte na **OK** k jeho vytvoření.

|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | cdnverify.mycdnendpoint        | Tato hodnota společně s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použití záznam CNAME používá alias.        |
|TTL     | 1        | 1 se používá pro 1 hodina        |
|Hodnota TTL jednotky     | Hodiny        | Hodiny se používají jako měření času         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Název DNS vytváříte alias, v tomto příkladu je název DNS cdnverify.adatumcdnendpoint.azureedge.net dostupné ve výchozím nastavení k účtu úložiště.        |

Přejděte zpět na koncový bod CDN kliknutím **sítě** > **profilů CDN**a vyberte váš profil CDN. Klikněte na tlačítko **+ vlastní domény** zadejte váš alias záznam CNAME bez předpony cdnverify a klikněte na tlačítko **přidat**.

Po dokončení tohoto kroku se vraťte do zóny DNS a vytvořte záznam CNAME bez cdnverify předponu.  Po tomto bodě se bezpečně odstranit záznam CNAME s předponou cdnverify. Další informace o síti CDN a jak nakonfigurovat vlastní doménu bez kroku zprostředkující registrace najdete v článku [mapy Azure CDN obsah k vlastní doméně](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nakonfigurovat zpětné DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).