---
title: Zabezpečení služby Azure blockchain
description: Koncepce přístupu k datům a zabezpečení Azure blockchain Service
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879594"
---
# <a name="azure-blockchain-service-security"></a>Zabezpečení služby Azure blockchain

Služba Azure blockchain využívá několik možností Azure, které zajistí zabezpečení a dostupnost vašich dat. Data jsou zabezpečená pomocí izolace, šifrování a ověřování.

## <a name="isolation"></a>Izolace

Prostředky služby Azure blockchain jsou izolované v privátní virtuální síti. Každá transakce a ověřovací uzel je virtuální počítač (VM). Virtuální počítače v jedné virtuální síti nemůžou komunikovat přímo s virtuálními počítači v jiné virtuální síti. Izolace zajišťuje, že komunikace zůstane soukromá v rámci virtuální sítě. Další informace o izolaci virtuálních sítí Azure najdete v tématu věnovaném [izolaci ve veřejném cloudu Azure](../../security/fundamentals/isolation-choices.md#networking-isolation).

![Diagram virtuální sítě](./media/data-security/vnet.png)

## <a name="encryption"></a>Šifrování

Uživatelská data se ukládají ve službě Azure Storage. Data uživatelů jsou v klidovém stavu zašifrovaná a v klidovém stavu a jejich zabezpečení a důvěrnost. Další informace najdete v tématu: [Azure Storage Průvodce zabezpečením](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Authentication

Transakce se dají odesílat do uzlů blockchain prostřednictvím koncového bodu RPC. Klienti komunikují s uzlem transakce pomocí reverzní proxy server, která zpracovává ověřování uživatelů a šifruje data prostřednictvím TLS.

![Diagram ověřování](./media/data-security/authentication.png)

Existují tři režimy ověřování pro přístup přes protokol RPC.

### <a name="basic-authentication"></a>Základní ověřování

Základní ověřování používá hlavičku ověřování protokolu HTTP obsahující uživatelské jméno a heslo. Uživatelské jméno je název uzlu blockchain. Heslo je nastaveno při zřizování člena nebo uzlu. Heslo lze změnit pomocí Azure Portal nebo CLI.

### <a name="access-keys"></a>Přístupové klíče

Přístupové klíče používají náhodně vygenerovaný řetězec, který je zahrnutý v adrese URL koncového bodu. Pomocí dvou přístupových klíčů můžete povolit rotaci klíčů. Klíče je možné znovu vygenerovat z Azure Portal a CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) používá ověřovací mechanismus založený na deklaracích identity, kde se uživatel ověřuje pomocí přihlašovacích údajů uživatele Azure AD pomocí služby Azure AD. Azure AD poskytuje cloudovou správu identit a umožňuje zákazníkům používat jedinou identitu napříč celým podnikem a přistupovat k aplikacím v cloudu. Služba Azure blockchain se integruje s Azure AD a povoluje federaci ID, jednotné přihlašování a vícefaktorové ověřování. Můžete přiřadit uživatele, skupiny a aplikační role ve vaší organizaci pro blockchain člena a přístup k uzlu.

Proxy klient služby Azure AD je k dispozici na [GitHubu](https://github.com/Microsoft/azure-blockchain-connector/releases). Klient proxy přesměruje uživatele na přihlašovací stránku Azure AD a získá token nosiče po úspěšném ověření. Následně se uživatel připojí ke koncovému bodu proxy klienta aplikace klienta Ethereem, jako je Geth nebo Truffle. Nakonec při odeslání transakce klientský proxy server vloží token nosiče v hlavičce protokolu HTTP a reverzní proxy ověří token pomocí protokolu OAuth.

## <a name="keys-and-ethereum-accounts"></a>Klíče a účty Ethereem

Při zřizování členu služby Azure blockchain se vygeneruje účet Ethereem a pár veřejného a privátního klíče. Privátní klíč se používá k posílání transakcí do blockchain. Účet Ethereem je poslední 20 bajtů hodnoty hash veřejného klíče. Účet Ethereem se také označuje jako kapesní.

Pár privátních a veřejných klíčů je uložen jako soubor klíče ve formátu JSON. Privátní klíč je zašifrovaný pomocí hesla zadaného při vytvoření služby blockchain hl.

Privátní klíče slouží k digitálnímu podepisování transakcí. V soukromém blockchainy představuje inteligentní kontrakt podepsaný soukromým klíčem identitu podepisujícího. K ověření platnosti signatury může příjemce porovnat veřejný klíč podepsaného s adresou vypočítanou z podpisu.

Klíče Constellation slouží k jednoznačné identifikaci uzlu kvora. Klíče Constellation jsou generovány v době zřizování uzlu a jsou zadány v parametru privateFor soukromé transakce v rámci kvora.

## <a name="next-steps"></a>Další kroky

Podívejte [se, jak nakonfigurovat přístup Azure Active Directory pro službu Azure blockchain](configure-aad.md).
