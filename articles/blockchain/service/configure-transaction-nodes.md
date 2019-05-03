---
title: Konfigurace uzlů transakce služby Azure Blockchain
description: Postup konfigurace služby Azure Blockchain transakce uzly
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027957"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurace uzlů transakce služby Azure Blockchain

K interakci se službou Azure Blockchain, provedete prostřednictvím připojení na jeden nebo více uzlů transakce v svůj blockchain člen.  Aby bylo možné pracovat s uzly transakce, je potřeba nakonfigurovat uzly pro přístup.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření Azure Blockchain člena](create-member.md)

## <a name="transaction-node-overview"></a>Přehled transakcí uzlu

Transakce uzlů se používají k odesílání blockchain transakce do služby Azure Blockchain přes veřejný koncový bod. Výchozí uzel transakce obsahuje privátní klíč Etherea účet zaregistrovaný v blockchainu a proto nejde odstranit.

Chcete-li zobrazit podrobnosti o uzlu transakce výchozí:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do vaší služby Azure Blockchain člena. Vyberte **transakce uzly**.

    ![Vyberte výchozí uzel transakce](./media/configure-transaction-nodes/nodes.png)

    Podrobnosti o přehledu patří veřejný koncový bod adresy a veřejný klíč.

## <a name="create-transaction-node"></a>Vytvořit uzel transakce

Můžete přidat až devět dalších transakce uzly do svůj blockchain člen pro celkem 10 uzlů transakce. Přidáním uzlů transakce můžete zvýšit škálovatelnost nebo distribuci zatížení. Například můžete mít koncový bod transakce uzel pro různé klientské aplikace.

Přidat uzel transakce:

1. Na webu Azure Portal, přejděte do vaší služby Azure Blockchain člena a vyberte **transakce uzly > Přidat**.
1. Zadejte nastavení pro nový uzel transakce.

    ![Přidání uzlu transakce](./media/configure-transaction-nodes/add-node.png)

    | Nastavení | Popis |
    |---------|-------------|
    | Název | Název uzlu transakce. Název slouží k vytvoření adresu serveru DNS pro koncový bod transakce uzlu. Například, `newnode-myblockchainmember.blockchain.azure.com`. Po vytvoření nelze změnit název uzlu. |
    | Heslo | Nastavte silné heslo. Použijte heslo pro přístup ke koncovému bodu uzlu transakce se základním ověřováním.

1. Vyberte **Vytvořit**.

    Nový uzel transakce zřizování trvá asi 10 minut. Další transakce uzly vám být naúčtovány náklady. Další informace o nákladech najdete v části [ceny za Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Koncové body

Transakce uzly mít jedinečný název DNS a veřejných koncových bodů.

Chcete-li zobrazit podrobnosti o koncovém bodu uzlem transakce:

1. Na webu Azure Portal, přejděte k jednomu z uzlů služby Azure Blockchain člen transakce a vyberte **přehled**.

    ![Koncové body](./media/configure-transaction-nodes/endpoints.png)

Koncové body uzlu transakce jsou v bezpečí a vyžadovat ověření. Můžete připojit ke koncovému bodu transakce pomocí ověřování Azure AD, základní ověřování pomocí protokolu HTTPS a pomocí přístupového klíče přes protokoly HTTPS či objektu websocket na straně přes protokol SSL.

### <a name="azure-active-directory-access-control"></a>Řízení přístupu k Azure Active Directory

Azure Blockchain transakce uzel koncové body služby podporují ověřování pomocí Azure Active Directory (Azure AD). Do vašeho koncového bodu můžete udělit uživatele, skupiny a objektu zabezpečení přístupu ke službám Azure AD.

Udělit Azure AD access řídit do vašeho koncového bodu:

1. Na webu Azure Portal, přejděte do vaší služby Azure Blockchain člena a vyberte **transakce uzly > řízení přístupu (IAM) > Přidat > Přidat přiřazení role**.
1. Vytvořte nové přiřazení role pro uživatele, skupiny nebo instanční objekt (aplikační role).

    ![Přidejte roli IAM](./media/configure-transaction-nodes/add-role.png)

    | Nastavení | Akce |
    |---------|-------------|
    | Role | Vyberte **vlastníka**, **Přispěvatel**, nebo **čtečky**.
    | Přiřadit přístup k | Vyberte **uživatele, skupinu nebo instanční objekt služby Azure AD**.
    | Vyberte | Vyhledejte uživatele, skupinu nebo instanční objekt, který chcete přidat.

1. Vyberte **Uložit** přidání přiřazení role.

Další informace o řízení přístupu Azure AD najdete v tématu [spravovat přístup k prostředkům Azure pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md)

Podrobnosti o tom, jak připojit pomocí ověřování Azure AD najdete v tématu [připojit k uzlu ověřování pomocí AAD](configure-aad.md).

### <a name="basic-authentication"></a>Základní ověřování

Pro základní ověřování pomocí protokolu HTTPS pověření uživatelského jména a hesla jsou předány v hlavičce protokolu HTTPS požadavku koncového bodu.

Na webu Azure Portal můžete zobrazit podrobnosti o koncovém bodu uzlem transakce základní ověřování. Přejděte do jednoho z uzlů služby Azure Blockchain člen transakce a vyberte **základní ověřování** v nastavení.

![Základní ověřování](./media/configure-transaction-nodes/basic.png)

Uživatelské jméno je název uzlu a nedá se změnit.

Chcete-li použít adresu URL, nahraďte \<heslo\> s heslem, nastavte při zřizování uzlu. Heslo můžete aktualizovat tak, že vyberete **resetovat heslo**.

### <a name="access-keys"></a>Přístupové klíče

Přístupový klíč pro přístup k ověření pomocí klíče, je součástí adresu URL koncového bodu. Při zřízení uzel transakce jsou generovány dva přístupové klíče. Buď přístupový klíč slouží k ověřování. Dva klíče umožňuje změnit a obměna klíčů.

Můžete zobrazit klíčové podrobnosti transakce uzlu přístup a kopírování adresy koncových bodů, které zahrnují přístupové klíče. Přejděte do jednoho z uzlů služby Azure Blockchain člen transakce a vyberte **přístupové klíče** v nastavení.

### <a name="firewall-rules"></a>Pravidla brány firewall

Pravidla brány firewall umožňují omezení adresy IP, které se může pokusit o ověření pro uzel transakce.  Pokud nebudou nakonfigurovaná žádná pravidla brány firewall pro uzel transakce, nelze získat žádné straně přístup.  

Chcete-li zobrazit transakce uzel pravidla brány firewall, přejděte k jednomu z uzlů služby Azure Blockchain člen transakce a vyberte **pravidla brány Firewall** v nastavení.

Můžete přidat pravidla brány firewall tak, že zadáte název pravidla, počáteční IP adresu a koncovou IP adresu **pravidla brány Firewall** mřížky.

![Pravidla brány firewall](./media/configure-transaction-nodes/firewall-rules.png)

Pokud chcete povolit:

* **Jedna IP adresa:** Konfigurace stejné IP adresy pro počáteční a koncovou IP adresu.
* **Rozsah IP adres:** Nakonfigurujte počáteční a koncovou rozsah IP adres. Například rozsah od 10.221.34.0 a končící 10.221.34.255 umožní celý 10.221.34.xxx podsítě.
* **Povolte všechny IP adresy:** Nakonfigurujte počáteční IP adresa na hodnotu 0.0.0.0 a koncovou IP adresu, která 255.255.255.255.

## <a name="connection-strings"></a>Připojovací řetězce

Syntaxe připojovacího řetězce pro uzel transakce je k dispozici pro základní ověřování nebo pomocí přístupových klíčů. Připojovací řetězce, včetně přístupových kláves přes protokol HTTPS a WebSockets jsou k dispozici.

Můžete zobrazit transakce uzel připojovacích řetězců a zkopírujte adresy koncových bodů. Přejděte do jednoho z uzlů služby Azure Blockchain člen transakce a vyberte **připojovací řetězce** v nastavení.

![Připojovací řetězce](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód je k dispozici rychle povolit připojování k uzlu prostřednictvím Web3, Nethereum, Web3js a Truffle transakce.

Můžete zobrazit transakce uzel ukázka připojovací kód a zkopírujte ho používat Oblíbené vývojářské nástroje. Přejděte do jednoho z uzlů služby Azure Blockchain člen transakce a vyberte **ukázkový kód** v nastavení.

Vyberte kartu Web3 nebo Nethereum zobrazíte vzorový kód, který chcete použít.

![Ukázka kódu](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace uzlů transakce pomocí Azure CLI](manage-cli.md)
