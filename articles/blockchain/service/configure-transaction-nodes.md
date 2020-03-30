---
title: Konfigurace transakčních uzlů služby Azure Blockchain Service
description: Jak nakonfigurovat uzly transakcí služby Azure Blockchain Service
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 4a9a4f660dd171e65b600ec4cd66714ca476b091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252256"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurace transakčních uzlů služby Azure Blockchain Service

Transakční uzly se používají k odesílání transakcí blockchain u služby Azure Blockchain Service prostřednictvím veřejného koncového bodu. Výchozí uzel transakce obsahuje soukromý klíč účtu Ethereum registrovaného v blockchainu a jako takový nelze odstranit.

Zobrazení podrobností o výchozím uzlu transakce:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na člena služby Azure Blockchain Service. Vyberte **uzly transakce**.

    ![Vybrat výchozí uzel transakce](./media/configure-transaction-nodes/nodes.png)

    Podrobnosti o přehledu zahrnují adresy veřejného koncového bodu a veřejný klíč.

## <a name="create-transaction-node"></a>Vytvořit uzel transakce

Do člena blockchainu můžete přidat až devět dalších transakčních uzlů, celkem 10 transakčních uzlů. Přidáním uzlů transakcí můžete zvýšit škálovatelnost nebo distribuovat zatížení. Můžete mít například koncový bod uzlu transakce pro různé klientské aplikace.

Přidání uzlu transakce:

1. Na webu Azure Portal přejděte na člena služby Azure Blockchain service a vyberte **uzly transakcí > Přidat**.
1. Dokončete nastavení nového uzlu transakce.

    ![Přidat uzel transakce](./media/configure-transaction-nodes/add-node.png)

    | Nastavení | Popis |
    |---------|-------------|
    | Name (Název) | Název uzlu transakce. Název se používá k vytvoření adresy DNS pro koncový bod uzlu transakce. Například, `newnode-myblockchainmember.blockchain.azure.com`. Název uzlu nelze změnit po jeho vytvoření. |
    | Heslo | Nastavte silné heslo. Pomocí hesla pro přístup ke koncovému bodu uzlu transakce se základním ověřováním.

1. Vyberte **Vytvořit**.

    Zřizování nového uzlu transakce trvá přibližně 10 minut. Dalším uzlem transakcí vznikají náklady. Další informace o nákladech najdete v tématu [Ceny Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Koncové body

Transakční uzly mají jedinečný název DNS a veřejné koncové body.

Zobrazení podrobností koncového bodu uzlu transakce:

1. Na webu Azure Portal přejděte na jeden z uzlů transakcí členů služby Azure Blockchain service a vyberte **Přehled**.

    ![Koncové body](./media/configure-transaction-nodes/endpoints.png)

Koncové body uzlu transakce jsou zabezpečené a vyžadují ověření. Ke koncovému bodu transakce se můžete připojit pomocí ověřování Azure AD, základního ověřování HTTPS a použití přístupového klíče přes protokol HTTPS nebo websocket přes protokol SSL.

### <a name="azure-active-directory-access-control"></a>Řízení přístupu služby Azure Active Directory

Koncové body transakčních uzlů Služby Azure Blockchain podporují ověřování Azure Active Directory (Azure AD). Uživateli, skupině a instančnímu objektu Azure AD můžete udělit přístup k koncovému bodu.

Udělení řízení přístupu azure ad do koncového bodu:

1. Na webu Azure Portal přejděte na člena služby Azure Blockchain service a vyberte **uzly transakcí > řízení přístupu (IAM) > Přidat > přidat přiřazení role**.
1. Vytvořte nové přiřazení role pro uživatele, skupinu nebo instanční objekt (role aplikace).

    ![Přidat roli IAM](./media/configure-transaction-nodes/add-role.png)

    | Nastavení | Akce |
    |---------|-------------|
    | Role | Vyberte **vlastníka**, **přispěvatele**nebo **čtenáře**.
    | Přiřadit přístup | Vyberte **uživatele, skupinu nebo instanční objekt Služby Azure AD**.
    | Vyberte | Vyhledejte uživatele, skupinu nebo instanční objekt, který chcete přidat.

1. Chcete-li přidat přiřazení role, vyberte **Uložit.**

Další informace o řízení přístupu k Azure AD najdete v [tématu Správa přístupu k prostředkům Azure pomocí RBAC a portálu Azure](../../role-based-access-control/role-assignments-portal.md)

Podrobnosti o tom, jak se připojit pomocí ověřování Azure AD, najdete [v tématu připojení k uzlu pomocí ověřování AAD](configure-aad.md).

### <a name="basic-authentication"></a>Základní ověřování

Pro základní ověřování HTTPS jsou pověření uživatelského jména a hesla předána v hlavičce HTTPS požadavku do koncového bodu.

Můžete zobrazit podrobnosti o základním ověřovacím koncovém bodu uzlu transakce na webu Azure Portal. Přejděte na jeden z vašich uzlů transakcí člena služby Azure Blockchain Service a v nastavení vyberte **Základní ověřování.**

![Základní ověřování](./media/configure-transaction-nodes/basic.png)

Uživatelské jméno je název uzlu a nelze jej změnit.

Chcete-li použít \<adresu\> URL, nahraďte heslo heslem nastaveným při zřízení uzlu. Heslo můžete aktualizovat výběrem možnosti **Obnovit heslo**.

### <a name="access-keys"></a>Přístupové klíče

Pro ověřování pomocí přístupového klíče je přístupový klíč součástí adresy URL koncového bodu. Při zřízení uzlu transakce jsou generovány dva přístupové klíče. Pro ověřování lze použít buď přístupový klíč. Dvě klávesy umožňují měnit a otáčet klávesy.

Můžete zobrazit podrobnosti o přístupovém klíči uzlu transakce a zkopírovat adresy koncových bodů, které obsahují přístupové klíče. Přejděte na jeden z vašich uzlů transakcí člena služby Azure Blockchain Service a v nastavení vyberte **Přístupové klíče.**

### <a name="firewall-rules"></a>Pravidla brány firewall

Pravidla brány firewall umožňují omezit adresy IP, které se mohou pokusit ověřit na uzlu transakce.  Pokud pro uzel transakce nejsou nakonfigurována žádná pravidla brány firewall, žádná strana k nim nemůže získat přístup.  

Chcete-li zobrazit pravidla brány firewall uzlu transakce, přejděte na jeden z uzlů transakcí člena služby Azure Blockchain Service a v nastavení vyberte **pravidla brány firewall.**

Pravidla brány firewall můžete přidat zadáním názvu pravidla, počáteční adresy IP a koncové adresy IP do mřížky **pravidel brány firewall.**

![Pravidla brány firewall](./media/configure-transaction-nodes/firewall-rules.png)

Chcete-li povolit:

* **Jedna IP adresa:** Nakonfigurujte stejnou adresu IP pro počáteční a koncovou adresu IP.
* **Rozsah IP adres:** Nakonfigurujte počáteční a koncový rozsah adres IP. Například rozsah začínající na 10.221.34.0 a končící na 10.221.34.255 by umožnil celou podsíť 10.221.34.xxx.
* **Povolit všechny adresy IP:** Nakonfigurujte počáteční adresu IP na 0.0.0.0 a koncovou adresu IP na 255.255.255.255.

## <a name="connection-strings"></a>Připojovací řetězce

Syntaxe připojovacího řetězce pro transakční uzel je k dispozici pro základní ověřování nebo použití přístupových klíčů. K dispozici jsou připojovací řetězce včetně přístupových klíčů přes protokol HTTPS a WebSockets.

Můžete zobrazit připojovací řetězce transakčního uzlu a zkopírovat adresy koncového bodu. Přejděte na jeden z uzlů transakcí členů služby Azure Blockchain Service a v nastavení vyberte **připojovací řetězce.**

![Připojovací řetězce](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód je k dispozici pro rychlé připojení k transakčnímu uzlu přes Web3, Nethereum, Web3js a Lanýž.

Ukázkový kód připojení uzlu transakce můžete zobrazit a zkopírovat pro použití s oblíbenými vývojářskými nástroji. Přejděte na jeden z uzlů transakcí členů služby Azure Blockchain service a v nastavení vyberte **Ukázkový kód.**

Zvolte kartu Web3, Nethereum, Lanýž nebo Web3j, abyste zobrazili ukázku kódu, kterou chcete použít.

![Ukázka kódu](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace uzlů transakcí pomocí rozhraní příkazového příkazu Azure](manage-cli.md)
