---
title: Delegování subdomény – Azure DNS
description: S tímto studijním programem můžete začít delegovat subdoménu Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: cd1443a9ca8ccf7172072078734f21d789d36194
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937439"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegování subdomény Azure DNS

Na portálu Azure můžete delegovat subdoménu DNS. Pokud například vlastníte contoso.com domény, můžete delegovat subdoménu nazvanou *inženýrství* do jiné samostatné zóny, kterou můžete spravovat odděleně od zóny contoso.com.

Pokud chcete, můžete delegovat subdoménu pomocí [Azure PowerShellu](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li delegovat subdoménu Azure DNS, musíte nejprve delegovat svou veřejnou doménu na Azure DNS. Pokyny, jak nakonfigurovat názvové servery pro delegování, najdete v tématu [Delegování domény na Azure DNS.](./dns-delegate-domain-azure-dns.md) Jakmile je vaše doména delegována do zóny Azure DNS, můžete delegovat svou subdoménu.

> [!NOTE]
> Contoso.com se používá jako příklad v celém tomto článku. Doménu contoso.com nahraďte vlastním názvem domény.

## <a name="create-a-zone-for-your-subdomain"></a>Vytvoření zóny pro subdoménu

Nejprve vytvořte zónu pro **inženýrskou** subdoménu.

1. Na portálu Azure vyberte **Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **DNS**a vyberte **zónu DNS**.
3. Vyberte **Vytvořit**.
4. Do podokna **Vytvořit zónu DNS** zadejte **engineering.contoso.com** do textového pole **Název.**
5. Vyberte skupinu prostředků pro zónu. Můžete chtít použít stejnou skupinu prostředků jako nadřazenou zónu k udržení podobných prostředků pohromadě.
6. Klikněte na **Vytvořit**.
7. Po úspěšném nasazení přejděte do nové zóny.

## <a name="note-the-name-servers"></a>Poznamenejte si názvové servery

Dále si všimněte čtyř názvových serverů pro inženýrskou subdoménu.

V podokně **inženýrské** zóny si poznamenejte čtyři názvové servery zóny. Tyto názvové servery budete používat později.

## <a name="create-a-test-record"></a>Vytvoření testovacího záznamu

Vytvořte záznam **A,** který se použije pro testování. Vytvořte například záznam **www** A a nakonfigurujte jej pomocí adresy **IP 10.10.10.10.**

## <a name="create-an-ns-record"></a>Vytvoření záznamu NS

Dále vytvořte záznam názvového serveru (NS) pro **inženýrskou** zónu.

1. Přejděte do zóny pro nadřazenou doménu.
2. Vyberte **+ Sada záznamů**.
3. V podokně **Přidat sadu záznamů** zadejte **inženýrství** do textového pole **Název.**
4. V **pole Typ**vyberte **položku NS**.
5. V **části Name server**zadejte čtyři názvové servery, které jste dříve zaznamenali z **inženýrské** zóny.
6. Klikněte na tlačítko **OK**.

## <a name="test-the-delegation"></a>Otestujte delegování

Použijte nslookup k testování delegování.

1. Otevřete okno PowerShellu.
2. Na příkazovém řádku zadejte`nslookup www.engineering.contoso.com.`
3. Měli byste obdržet neautoritativní odpověď s adresou **10.10.10.10**.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nakonfigurovat reverzní DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).