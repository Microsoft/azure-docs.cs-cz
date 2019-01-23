---
title: Delegate an Azure DNS subdomain
description: Naučíte se delegovat subdoménu Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/22/2019
ms.author: victorh
ms.openlocfilehash: 87a80703c473245660a850645ca3fef21bbd80f6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452713"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegate an Azure DNS subdomain

Na webu Azure portal můžete použít pro delegování subdoméně DNS. Například pokud jste vlastníkem domény contoso.com, můžete subdoménu delegovat volá *engineering* jiný, samostatný zóny, který může být spravována odděleně od zónu contoso.com.

## <a name="prerequisites"></a>Požadavky

Delegovat subdoménu Azure DNS, musí nejprve přenést svoji veřejnou doménu do Azure DNS. Zobrazit [delegování domény do Azure DNS](./dns-delegate-domain-azure-dns.md) pokyny ke konfiguraci vaší názvové servery pro delegování. Once your domain is delegated to your Azure DNS zone, you can delegate your subdomain.

V příkladech v tomto článku se používá doménu contoso.com. Při použití těchto postupů musíte nahradit vlastní domény.

## <a name="create-a-zone-for-your-subdomain"></a>Create a zone for your subdomain

Nejprve vytvořit zónu **engineering** subdomény.

1. Na webu Azure Portal, vyberte **vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **DNS**a vyberte **zónu DNS**.
3. Vyberte **Vytvořit**.
4. V **vytvořit zónu DNS** podokně, zadejte **engineering.contoso.com** v **název** textového pole.
5. Vyberte skupinu prostředků pro vaši zónu. Můžete chtít použít stejnou skupinu prostředků jako nadřazenou zónu společně zachovat stejné prostředky.
6. Klikněte na možnost **Vytvořit**.
7. Po úspěšném nasazení, přejděte do nové zóny.

## <a name="note-the-name-servers"></a>Mějte na paměti názvové servery

Next, copy the four name servers for your subdomain.

1. Na **engineering** zóna podokně, mějte na paměti čtyři názvové servery pro zónu. Tyto názvové servery budete používat později.
2. Vytvoření **A** záznam, který chcete použít pro testování. Například vytvořit **www** A zaznamenávat a u něho nakonfigurujte **10.10.10.10** IP adresu.

## <a name="create-an-ns-record"></a>Vytvoření záznamů NS

V dalším kroku vytvoření záznamu názvového serveru (NS) pro **engineering** zóny.

1. Přejděte k zóně pro nadřazené domény.
2. Vyberte **+ Sada záznamů**.
3. Na **přidat sadu záznamů** podokně, zadejte **engineering** v **název** textového pole.
4. Pro **typ**vyberte **NS**.
5. V části **názvový server**, zadat čtyři názvové servery, které jste si dříve poznamenali z **engineering** zóny.
6. Klikněte na **OK**.

## <a name="test-the-delegation"></a>Test delegování

Nslookup použijte k otestování delegování.

1. Otevřete okno Powershellu.
2. Na příkazovém řádku zadejte `nslookup www.engineering.<your domain name>.`
3. Měli byste obdržet neautoritativní odpovědí, který zobrazuje adresu **10.10.10.10**.



## <a name="next-steps"></a>Další postup

Zjistěte, jak [konfigurace reverzních záznamů DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).