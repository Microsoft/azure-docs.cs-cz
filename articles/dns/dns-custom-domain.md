---
title: Integrace Azure DNS s prostředky Azure | Dokumentace Microsoftu
description: Zjistěte, jak používat Azure DNS podél zajištění DNS pro prostředky Azure.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/19/2018
ms.author: victorh
ms.openlocfilehash: 8e8a09ede66213247b306c77938dbff30651fee5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727144"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Použití Azure DNS k poskytování nastavení vlastní domény pro službu Azure

Azure DNS poskytuje DNS pro vlastní doménu pro některý z vašich prostředků Azure, že podpora vlastní domény nebo které mají plně kvalifikovaný název domény (FQDN). Příkladem je máte webovou aplikaci Azure a chcete, aby uživatelé pro přístup k ní buď používá plně kvalifikovaný název domény contoso.com nebo www.contoso.com. Tento článek vás provede konfigurací služby Azure s využitím Azure DNS pro používání vlastní domény.

## <a name="prerequisites"></a>Požadavky

Chcete-li používat Azure DNS pro vaši vlastní doménu, musíte nejprve delegování domény do Azure DNS. Navštivte [delegování domény do Azure DNS](./dns-delegate-domain-azure-dns.md) pokyny ke konfiguraci vaší názvové servery pro delegování. Jakmile se vaše doména se deleguje na zónu Azure DNS, budete moct nakonfigurovat záznamy DNS, které jsou potřeba.

Můžete nakonfigurovat individuální nebo vlastní doménu pro [aplikace Azure Function App](#azure-function-app), [veřejné IP adresy](#public-ip-address), [služby App Service (webové aplikace)](#app-service-web-apps), [Blob storage](#blob-storage), a [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Aplikace Azure Function App

Konfigurace vlastní domény pro aplikace Azure function App, je vytvořili záznam CNAME, tak i konfigurace v samotné aplikaci function app.
 
Přejděte do **jiných** > **aplikace Function App** a vyberte aplikaci funkcí. Klikněte na tlačítko **funkce platformy** a v části **sítě** klikněte na tlačítko **vlastní domény**.

![okno app – funkce](./media/dns-custom-domain/functionapp.png)

Všimněte si aktuální adresa url na **vlastní domény** okno, tato adresa se používá jako alias pro záznam DNS vytvořený.

![okně vlastní domény](./media/dns-custom-domain/functionshostname.png)

Přejděte na svoji zónu DNS a klikněte na tlačítko **+ sada záznamů**. Vyplňte následující informace o **přidat sadu záznamů** okna a kliknutím na **OK** k jeho vytvoření.

|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | myfunctionapp        | Tato hodnota spolu s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použít záznam CNAME, který používá jako alias.        |
|TTL     | 1        | použije se 1 za 1 hodinu        |
|Jednotka TTL     | Hodiny        | Hodiny jsou použity jako měření času         |
|Alias     | adatumfunction.azurewebsites.net        | Název DNS vytvoříte alias, v tomto příkladu je název DNS adatumfunction.azurewebsites.net dostupné ve výchozím nastavení aplikace function App.        |

Přejděte zpět do aplikace function app, klikněte na **funkce platformy**a v části **sítě** klikněte na tlačítko **vlastní domény**, pak v oblasti **názvy hostitelů**klikněte na tlačítko **+ přidat název hostitele**.

Na **přidat název hostitele** okně zadejte záznam CNAME v **hostname** textové pole a klikněte na tlačítko **ověřit**. Pokud záznam byl schopen najít, **přidat název hostitele** se zobrazí tlačítko. Klikněte na tlačítko **přidat název hostitele** přidat alias.

![aplikace Function App přidat název okna hostitele](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Veřejná IP adresa

Konfigurace vlastní domény pro služby, které používají veřejné IP adresy prostředků, jako je například služba Application Gateway, nástroje pro vyrovnávání zatížení, cloudové služby, virtuální počítače Resource Manageru, přičemž klasické virtuální počítače, záznam CNAME se zaznamenat použít.

Přejděte do **sítě** > **veřejnou IP adresu**, vyberte prostředek veřejné IP adresy a klikněte na tlačítko **konfigurace**. Zaznamenání IP adresou uvedenou.

![okno veřejné IP adresy](./media/dns-custom-domain/publicip.png)

Přejděte na svoji zónu DNS a klikněte na tlačítko **+ sada záznamů**. Vyplňte následující informace o **přidat sadu záznamů** okna a kliknutím na **OK** k jeho vytvoření.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | mywebserver        | Tato hodnota spolu s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | A        | Záznam A použijte, protože prostředek IP adresy.        |
|TTL     | 1        | použije se 1 za 1 hodinu        |
|Jednotka TTL     | Hodiny        | Hodiny jsou použity jako měření času         |
|IP adresa     | <your ip address>       | Veřejnou IP adresu.|

![Vytvoření záznamu A](./media/dns-custom-domain/arecord.png)

Jakmile se vytvoří záznam A, spusťte `nslookup` ověření záznamů řeší.

![vyhledávání dns pro veřejné IP adresy](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (webové aplikace)

Následující kroky vás provedou konfigurace vlastní domény k webové aplikaci app service.

Přejděte do **Web a mobilní zařízení** > **služby App Service** a vyberte prostředek, je konfigurace vlastního názvu domény a klikněte na tlačítko **vlastní domény**.

Všimněte si aktuální adresa url na **vlastní domény** okno, tato adresa se používá jako alias pro záznam DNS vytvořený.

![Okno vlastních domén](./media/dns-custom-domain/url.png)

Přejděte na svoji zónu DNS a klikněte na tlačítko **+ sada záznamů**. Vyplňte následující informace o **přidat sadu záznamů** okna a kliknutím na **OK** k jeho vytvoření.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | mywebserver        | Tato hodnota spolu s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použít záznam CNAME, který používá jako alias. Pokud prostředek IP adresy, záznam se použije.        |
|TTL     | 1        | použije se 1 za 1 hodinu        |
|Jednotka TTL     | Hodiny        | Hodiny jsou použity jako měření času         |
|Alias     | webserver.azurewebsites.net        | Název DNS vytvoříte alias, v tomto příkladu je název DNS webserver.azurewebsites.net dostupné ve výchozím nastavení do webové aplikace.        |


![Vytvoření záznamu CNAME](./media/dns-custom-domain/createcnamerecord.png)

Přejděte zpět do služby app service, který je nakonfigurovaný pro název vlastní domény. Klikněte na tlačítko **vlastní domény**, pak klikněte na tlačítko **názvy hostitelů**. Přidejte záznam CNAME, který jste vytvořili, klikněte na tlačítko **+ přidat název hostitele**.

![Obrázek 1](./media/dns-custom-domain/figure1.png)

Po dokončení tohoto procesu se spuštěním **nslookup** ověřte překlad názvů funguje.

![Obrázek 1](./media/dns-custom-domain/finalnslookup.png)

Další informace o mapování vlastní domény do služby App Service, najdete v tématu [mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Pokud budete muset koupit vlastní doménu, navštivte [nákup vlastního názvu domény pro Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md) získat další informace o domény služby App Service.

## <a name="blob-storage"></a>Blob Storage

Následující kroky vás provedou konfigurací záznam CNAME pro účet úložiště blob pomocí metody asverify. Tato metoda zajišťuje, že neexistuje žádný výpadek.

Přejděte do **úložiště** > **účty úložiště**, vyberte svůj účet úložiště a klikněte na tlačítko **vlastní domény**. Zaznamenání, plně kvalifikovaný název domény v kroku 2, tato hodnota se používá k vytvoření první záznamu CNAME

![vlastní doména úložiště objektů BLOB](./media/dns-custom-domain/blobcustomdomain.png)

Přejděte na svoji zónu DNS a klikněte na tlačítko **+ sada záznamů**. Vyplňte následující informace o **přidat sadu záznamů** okna a kliknutím na **OK** k jeho vytvoření.


|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | asverify.mystorageaccount        | Tato hodnota spolu s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použít záznam CNAME, který používá jako alias.        |
|TTL     | 1        | použije se 1 za 1 hodinu        |
|Jednotka TTL     | Hodiny        | Hodiny jsou použity jako měření času         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Název DNS vytvoříte alias, v tomto příkladu je název DNS asverify.adatumfunctiona9ed.blob.core.windows.net dostupné ve výchozím nastavení do účtu úložiště.        |

Přejděte zpět na svůj účet úložiště kliknutím **úložiště** > **účty úložiště**, vyberte svůj účet úložiště a klikněte na tlačítko **vlastní domény**. Typ v aliasem, který jste vytvořili bez předpony bude v textovém poli zaškrtněte ** použít nepřímé ověření CNAME a klikněte na tlačítko **Uložit**. Po dokončení tohoto kroku se vraťte do zóny DNS a vytvořte záznam CNAME bez předpony asverify.  Po tomto bodu, můžete je bezpečně odstranit záznam CNAME cdnverify předponu.

![vlastní doména úložiště objektů BLOB](./media/dns-custom-domain/indirectvalidate.png)

Ověřte překlad DNS tak, že spuštění `nslookup`

Další informace o mapování vlastní domény do koncového bodu blob storage najdete tady: [konfigurace vlastního názvu domény pro koncový bod služby Blob storage](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Následující kroky vás provedou konfigurací záznam CNAME pro koncový bod CDN pomocí metody cdnverify. Tato metoda zajišťuje, že neexistuje žádný výpadek.

Přejděte do **sítě** > **profily CDN**, vyberte váš profil CDN a klikněte na tlačítko **koncové body** pod **Obecné**.

Vyberte koncový bod ve spolupráci s a klikněte na tlačítko **+ vlastní doména**. Poznámka: **název hostitele koncového bodu** jako tato hodnota je záznam CNAME odkazující na záznam.

![Vlastní doména CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Přejděte na svoji zónu DNS a klikněte na tlačítko **+ sada záznamů**. Vyplňte následující informace o **přidat sadu záznamů** okna a kliknutím na **OK** k jeho vytvoření.

|Vlastnost  |Hodnota  |Popis  |
|---------|---------|---------|
|Název     | cdnverify.mycdnendpoint        | Tato hodnota spolu s Popisek názvu domény je plně kvalifikovaný název domény pro vlastní název domény.        |
|Typ     | CNAME        | Použít záznam CNAME, který používá jako alias.        |
|TTL     | 1        | použije se 1 za 1 hodinu        |
|Jednotka TTL     | Hodiny        | Hodiny jsou použity jako měření času         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Název DNS vytvoříte alias, v tomto příkladu je název DNS cdnverify.adatumcdnendpoint.azureedge.net dostupné ve výchozím nastavení do účtu úložiště.        |

Přejděte zpět na váš koncový bod CDN kliknutím **sítě** > **profily CDN**a vyberte váš profil CDN. Klikněte na tlačítko **+ vlastní doména** a zadejte váš alias záznam CNAME bez předpony cdnverify a klikněte na tlačítko **přidat**.

Po dokončení tohoto kroku se vraťte do zóny DNS a vytvořte záznam CNAME bez předpony cdnverify.  Po tomto bodu, můžete je bezpečně odstranit záznam CNAME cdnverify předponu. Další informace o síti CDN a jak nakonfigurovat vlastní doméně, aniž byste kroku zprostředkující registrace najdete [mapování obsahu Azure CDN na vlastní doménu](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Další postup

Zjistěte, jak [konfigurace reverzních záznamů DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).