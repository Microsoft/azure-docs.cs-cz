---
title: Konfigurace uzlů transakcí služby Azure blockchain
description: Jak nakonfigurovat uzly transakcí služby Azure blockchain
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 0b659fa9f15cd41e9e55de526747d470046cafa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091255"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurace uzlů transakcí služby Azure blockchain

Uzly transakce slouží k posílání transakcí blockchain do služby Azure blockchain prostřednictvím veřejného koncového bodu. Výchozí uzel transakce obsahuje privátní klíč účtu Ethereem zaregistrovaného na blockchain a takovým způsobem jej nelze odstranit.

Zobrazení podrobností o výchozích uzlech transakce:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte ke členu služby Azure blockchain. Vyberte **uzly transakce**.

    ![Vybrat výchozí uzel transakce](./media/configure-transaction-nodes/nodes.png)

    Souhrnné informace obsahují adresy veřejných koncových bodů a veřejný klíč.

## <a name="create-transaction-node"></a>Vytvořit uzel transakce

Do blockchain člena můžete přidat až devět dalších uzlů transakcí, a to za celkem 10 uzlů transakcí. Přidáním uzlů transakce můžete zvýšit škálovatelnost nebo distribuovat zatížení. Například můžete mít koncový bod uzlu transakce pro různé klientské aplikace.

Chcete-li přidat uzel transakce:

1. V Azure Portal přejděte ke členu služby Azure blockchain a vyberte **uzly transakcí > přidat**.
1. Dokončete nastavení pro nový uzel transakce.

    ![Přidat uzel transakce](./media/configure-transaction-nodes/add-node.png)

    | Nastavení | Popis |
    |---------|-------------|
    | Název | Název uzlu transakce. Název slouží k vytvoření adresy DNS pro koncový bod uzlu transakce. Například, `newnode-myblockchainmember.blockchain.azure.com`. Název uzlu po jeho vytvoření nelze změnit. |
    | Heslo | Nastavte silné heslo. Použijte heslo pro přístup ke koncovému bodu uzlu transakce pomocí základního ověřování.

1. Vyberte **Vytvořit**.

    Zřizování nového uzlu transakce trvá přibližně 10 minut. K dalším uzlům transakce se účtují náklady. Další informace o cenách najdete v tématu [ceny Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Koncové body

Uzly transakce mají jedinečný název DNS a veřejné koncové body.

Chcete-li zobrazit podrobnosti o koncovém bodu uzlu transakce:

1. V Azure Portal přejděte na jeden z uzlů transakce člena služby Azure blockchain a vyberte **Přehled**.

    ![Snímek obrazovky zobrazuje přehled pro uzly transakcí pro člena blockchain.](./media/configure-transaction-nodes/endpoints.png)

Koncové body uzlu transakce jsou zabezpečené a vyžadují ověření. Můžete se připojit ke koncovému bodu transakce pomocí ověřování Azure AD, základního ověřování HTTPS a pomocí přístupového klíče přes HTTPS nebo WebSocket přes TLS.

### <a name="azure-active-directory-access-control"></a>Řízení přístupu Azure Active Directory

Koncové body uzlu transakce služby Azure blockchain podporují ověřování Azure Active Directory (Azure AD). K vašemu koncovému bodu můžete udělit přístup uživatele, skupiny a instančního objektu služby Azure AD.

Udělení řízení přístupu Azure AD ke koncovému bodu:

1. V Azure Portal přejděte ke členu služby Azure blockchain a vyberte **uzly transakcí > řízení přístupu (IAM) > přidejte > přidání přiřazení role**.
1. Vytvoří nové přiřazení role pro uživatele, skupinu nebo instanční objekt (aplikační role).

    ![Přidat roli IAM](./media/configure-transaction-nodes/add-role.png)

    | Nastavení | Akce |
    |---------|-------------|
    | Role | Vyberte možnost **vlastník**, **Přispěvatel** nebo **Čtenář**.
    | Přiřadit přístup | Vyberte **uživatele, skupinu nebo instanční objekt služby Azure AD**.
    | Vyberte | Vyhledejte uživatele, skupinu nebo instanční objekt, který chcete přidat.

1. Vyberte **Uložit** a přidejte přiřazení role.

Další informace o řízení přístupu Azure AD najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md)

Podrobnosti o tom, jak se připojit pomocí ověřování Azure AD, najdete v tématu [připojení k uzlu pomocí ověřování AAD](configure-aad.md).

### <a name="basic-authentication"></a>Základní ověřování

Pro základní ověřování protokolem HTTPS se přihlašovací údaje uživatelského jména a hesla předávají v hlavičce HTTPS žádosti do koncového bodu.

Podrobnosti koncového bodu základního ověřování uzlu transakce můžete zobrazit v Azure Portal. Přejděte na jeden z uzlů transakcí členů služby Azure blockchain a v nastavení vyberte **základní ověřování** .

![Základní ověřování](./media/configure-transaction-nodes/basic.png)

Uživatelské jméno je název vašeho uzlu a nelze ho změnit.

Chcete-li použít adresu URL, nahraďte \<password\> heslo nastavené při zřízení uzlu. Heslo můžete aktualizovat výběrem možnosti **resetovat heslo**.

### <a name="access-keys"></a>Přístupové klíče

Pro ověřování přístupového klíče je přístupový klíč obsažený v adrese URL koncového bodu. Po zřízení uzlu transakce jsou vygenerovány dva přístupové klíče. Pro ověřování lze použít buď přístupový klíč. Dva klíče umožňují měnit a střídat klíče.

Můžete zobrazit podrobnosti přístupového klíče uzlu transakce a kopírovat adresy koncových bodů, které obsahují přístupové klíče. Přejděte na jeden z uzlů transakcí členů služby Azure blockchain a vyberte **přístupové klíče** v nastavení.

### <a name="firewall-rules"></a>Pravidla brány firewall

Pravidla brány firewall umožňují omezit IP adresy, které se mohou pokusit ověřit na váš uzel transakce.  Pokud nejsou nakonfigurována žádná pravidla brány firewall pro váš uzel transakce, nemůže být k ní mít k dispozici žádná ze stran.  

Pokud chcete zobrazit pravidla brány firewall pro uzel transakce, přejděte na jeden z uzlů transakcí členů služby Azure blockchain a v části nastavení vyberte **pravidla brány firewall** .

Pravidla brány firewall můžete přidat zadáním názvu pravidla, počáteční IP adresy a koncové IP adresy v mřížce **pravidla brány firewall** .

![Pravidla brány firewall](./media/configure-transaction-nodes/firewall-rules.png)

Povolení:

* **Jedna IP adresa:** Nakonfigurujte stejnou IP adresu pro počáteční a koncové IP adresy.
* **Rozsah IP adres:** Nakonfigurujte počáteční a koncový rozsah IP adres. Například rozsah začínající na 10.221.34.0 a končící na 10.221.34.255 by povoloval celou 10.221.34.xxx podsíť.
* **Povolení všech IP adres:** Nakonfigurujte počáteční IP adresu na 0.0.0.0 a koncovou IP adresu 255.255.255.255.

## <a name="connection-strings"></a>Připojovací řetězce

Syntaxe připojovacího řetězce pro váš uzel transakce je k dispozici pro základní ověřování nebo pro používání přístupových klíčů. K dispozici jsou připojovací řetězce, včetně přístupových klíčů přes HTTPS a WebSockets.

Můžete zobrazit připojovací řetězce uzlu transakce a kopírovat adresy koncových bodů. Přejděte na jeden z uzlů transakcí členů služby Azure blockchain a v nastavení vyberte **připojovací řetězce** .

![Připojovací řetězce](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód je k dispozici pro rychlé povolení připojení k vašemu transakčnímu uzlu prostřednictvím Web3, Nethereum, Web3js a Truffle.

Můžete zobrazit ukázkový kód připojení uzlu transakce a zkopírovat ho pro použití s oblíbenými vývojářskými nástroji. Přejít na jeden z uzlů transakcí členů služby Azure blockchain a v nastavení vyberte **vzorový kód** .

Vyberte kartu Web3, Nethereum, Truffle nebo Web3j a zobrazte ukázku kódu, který chcete použít.

![Ukázka kódu](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace uzlů transakcí pomocí Azure CLI](manage-cli.md)
