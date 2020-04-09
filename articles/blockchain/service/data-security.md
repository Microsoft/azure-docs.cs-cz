---
title: Zabezpečení služby Azure Blockchain Service
description: Koncepty přístupu k datům a zabezpečení služby Azure Blockchain Service
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879594"
---
# <a name="azure-blockchain-service-security"></a>Zabezpečení služby Azure Blockchain Service

Služba Azure Blockchain používá několik funkcí Azure, které zajišťují zabezpečení a dostupnost vašich dat. Data jsou zabezpečena pomocí izolace, šifrování a ověřování.

## <a name="isolation"></a>Izolace

Prostředky služby Azure Blockchain Service jsou izolované v privátní virtuální síti. Každý uzel transakce a ověření je virtuální počítač (VM). Virtuální počítače v jedné virtuální síti nemohou komunikovat přímo s virtuálními počítači v jiné virtuální síti. Izolace zajišťuje komunikaci zůstane privátní v rámci virtuální sítě. Další informace o izolaci virtuální sítě Azure najdete [v tématu izolace ve veřejném cloudu Azure](../../security/fundamentals/isolation-choices.md#networking-isolation).

![Diagram virtuální sítě](./media/data-security/vnet.png)

## <a name="encryption"></a>Šifrování

Uživatelská data se ukládají v úložišti Azure. Uživatelská data jsou šifrována v pohybu a v klidovém stavu z důvodu bezpečnosti a důvěrnosti. Další informace najdete v tématu: [Průvodce zabezpečením azure storage](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Authentication

Transakce lze odesílat do uzlů blockchain prostřednictvím koncového bodu RPC. Klienti komunikují s transakčním uzlem pomocí reverzního proxy serveru, který zpracovává ověřování uživatelů a šifruje data přes TLS.

![Ověřovací diagram](./media/data-security/authentication.png)

Existují tři režimy ověřování pro přístup vzdáleného volání procedur.

### <a name="basic-authentication"></a>Základní ověřování

Základní ověřování používá ověřovací hlavičku HTTP obsahující uživatelské jméno a heslo. Uživatelské jméno je název uzlu blockchain. Heslo je nastaveno během zřizování člena nebo uzlu. Heslo lze změnit pomocí portálu Azure nebo cli.

### <a name="access-keys"></a>Přístupové klíče

Přístupové klávesy používají náhodně generovaný řetězec obsažený v adrese URL koncového bodu. Dva přístupové klávesy pomáhají povolit střídání klíčů. Klíče lze obnovit z portálu Azure a CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) používá mechanismus ověřování na základě deklarací, kde je uživatel ověřen službou Azure AD pomocí přihlašovacích údajů uživatele Azure AD. Azure AD poskytuje cloudovou správu identit a umožňuje zákazníkům používat jednu identitu v celém podniku a přistupovat k aplikacím v cloudu. Služba Azure Blockchain se integruje s Azure AD, která umožňuje federační federaci ID, jednotné přihlašování a vícefaktorové ověřování. Uživatelům, skupinám a rolím aplikací ve vaší organizaci můžete přiřadit přístup k členům blockchainu a uzlům.

Proxy klienta Azure AD je k dispozici na [GitHubu](https://github.com/Microsoft/azure-blockchain-connector/releases). Klient proxy přesměruje uživatele na přihlašovací stránku Azure AD a získá token nosiče po úspěšném ověření. Následně uživatel připojí klientskou aplikaci Ethereum, jako je Geth nebo Lanýž, ke koncovému bodu proxy klienta. Nakonec při odeslání transakce klientský proxy server vloží token nosiče v záhlaví http a reverzní proxy ověří token pomocí protokolu OAuth.

## <a name="keys-and-ethereum-accounts"></a>Keys a ethereum účty

Při zřizování člena služby Azure Blockchain Service se vygeneruje účet Ethereum a pár veřejného a soukromého klíče. Soukromý klíč se používá k odesílání transakcí do blockchainu. Účet Ethereum je posledních 20 bajtů hash veřejného klíče. Ethereum účet se také nazývá peněženka.

Dvojice soukromého a veřejného klíče je uložena jako soubor klíčů ve formátu JSON. Soukromý klíč je šifrován pomocí hesla zadaného při vytvoření služby blockchain ledger.

Soukromé klíče se používají k digitálnímu podepisování transakcí. V soukromých blockchainech představuje inteligentní smlouva podepsaná soukromým klíčem identitu podepisujícího. Chcete-li ověřit platnost podpisu, příjemce může porovnat veřejný klíč podepisující osoby s adresou vypočítanou z podpisu.

Klíče souhvězdí se používají k jednoznačné identifikaci uzlu Kvora. Souhvězdí klíče jsou generovány v době zřizování uzlu a jsou určeny v privateFor parametr soukromé transakce v kvoru.

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak nakonfigurovat přístup služby Azure Active Directory pro službu Azure Blockchain](configure-aad.md).
