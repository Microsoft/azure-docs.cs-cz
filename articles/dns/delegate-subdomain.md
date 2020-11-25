---
title: Delegování subdomény – Azure DNS
description: Pomocí této cesty výukového programu začněte delegovat Azure DNS subdoménou.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011536"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegování Azure DNS subdomény

K delegování subdomény DNS můžete použít Azure Portal. Pokud například vlastníte doménu contoso.com, můžete poddoménu s názvem *inženýry* delegovat na jinou a samostatnou zónu, kterou můžete spravovat odděleně od zóny contoso.com.

Pokud budete chtít, můžete subdoménu delegovat pomocí [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li delegovat Azure DNS subdoménu, musíte nejprve delegovat veřejnou doménu na Azure DNS. Pokyny ke konfiguraci názvových serverů pro delegování najdete v tématu [delegování domény na Azure DNS](./dns-delegate-domain-azure-dns.md) . Jakmile je vaše doména delegovaná do vaší Azure DNS zóny, můžete svou subdoménu delegovat.

> [!NOTE]
> Contoso.com se používá jako příklad v celém rámci tohoto článku. Doménu contoso.com nahraďte vlastním názvem domény.

## <a name="create-a-zone-for-your-subdomain"></a>Vytvořit zónu pro subdoménu

Nejprve vytvořte zónu pro subdoménu **strojírenství** .

1. Z Azure Portal vyberte **vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **DNS** a vyberte **zónu DNS**.
3. Vyberte **Vytvořit**.
4. V podokně **vytvořit ZÓNU DNS** zadejte do textového pole **název** **Engineering.contoso.com** .
5. Vyberte skupinu prostředků pro vaši zónu. Můžete chtít použít stejnou skupinu prostředků jako nadřazenou zónu pro zachování podobných prostředků dohromady.
6. Klikněte na **Vytvořit**.
7. Po úspěšném nasazení přejdete do nové zóny.

## <a name="note-the-name-servers"></a>Poznamenejte si názvové servery

Dále si poznamenejte čtyři názvové servery pro inženýrskou subdoménu.

V podokně **Technická** zóna si poznamenejte čtyři názvové servery zóny. Tyto názvové servery budete používat později.

## <a name="create-a-test-record"></a>Vytvořit záznam testu

Vytvořte záznam **a** , který chcete použít pro testování. Můžete například vytvořit záznam o **www** a a nakonfigurovat ho pomocí IP adresy **10.10.10.10** .

## <a name="create-an-ns-record"></a>Vytvoření záznamu NS

Dále vytvořte záznam názvového serveru (NS) pro **technickou** zónu.

1. Přejděte do zóny pro nadřazenou doménu.
2. Vyberte **+ Sada záznamů**.
3. V podokně **Přidat sadu záznamů** zadejte do textového pole **název** text **Engineering** .
4. Jako **typ** vyberte **NS**.
5. V části **názvový server** zadejte čtyři názvové servery, které jste si dříve poznamenali z **technického** pásma.
6. Klikněte na **OK**.

## <a name="test-the-delegation"></a>Testování delegování

K otestování delegování použijte nástroj Nslookup.

1. Otevřete okno PowerShellu.
2. Na příkazovém řádku zadejte `nslookup www.engineering.contoso.com.`
3. Měli byste obdržet neautoritativní odpověď ukazující na adresu **10.10.10.10**.

## <a name="next-steps"></a>Další kroky

Naučte se [Konfigurovat reverzní DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).