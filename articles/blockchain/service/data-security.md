---
title: Azure Blockchain služby zabezpečení
description: Azure Blockchain dat přístup a zabezpečení koncepty služby
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028197"
---
# <a name="azure-blockchain-service-security"></a>Azure Blockchain služby zabezpečení

Služba Azure Blockchain využívá několik možnosti Azure k uložení svých dat. zabezpečených a dostupných. Data zabezpečená pomocí izolace, šifrování a ověřování.

## <a name="isolation"></a>Izolace

Izolaci prostředků Azure služba Blockchain v privátní virtuální síť. Každá transakce a ověření uzel je virtuální počítač (VM). Virtuální počítače v jedné virtuální síti nemůžou komunikovat přímo do virtuálních počítačů v jiné virtuální sítě. Izolace zajistí, že komunikace zůstalo soukromý v rámci virtuální sítě. Další informace o izolaci virtuálních sítí Azure najdete v tématu [izolace ve veřejném cloudu Azure](../../security/azure-isolation.md#networking-isolation).

![Diagram virtuální sítě](./media/data-security/vnet.png)

## <a name="encryption"></a>Šifrování

Data uživatele uložená ve službě Azure storage. Uživatel data se šifrují přenášená i neaktivní uložená pro zabezpečení a zachováním důvěrnosti. Další informace naleznete v tématu: [Průvodci zabezpečením Azure Storage](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Authentication

Transakce je odeslat do uzlů blockchain prostřednictvím koncového bodu protokolu RPC. Klienti komunikovat s uzlem transakce pomocí reverzního proxy serveru, že ověřování uživatele obslužné rutiny a šifruje data přes protokol SSL.

![Diagram ověřování](./media/data-security/authentication.png)

Existují tři režimy ověřování pro přístup ke službě RPC.

### <a name="basic-authentication"></a>Základní ověřování

Základní ověřování používá autentifikační hlavička protokolu HTTP obsahující uživatelské jméno a heslo. Uživatelské jméno je název uzlu blockchain. Heslo je nastaveno během zřizování člena nebo uzel. Heslo lze změnit pomocí webu Azure portal nebo rozhraní příkazového řádku.

### <a name="access-keys"></a>Přístupové klíče

Přístupové klíče pomocí náhodně generované řetězce do adresy URL koncového bodu. Dva přístupové klíče pomáhal obměny klíče. Klíče můžete znovu vygenerovány z webu Azure portal a rozhraní příkazového řádku.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) podle deklarací identity používá ověřovací mechanismus kde je uživatel ověřený službou Azure AD pomocí přihlašovacích údajů uživatele Azure AD. Azure AD poskytuje správu identit v cloudu a umožňuje zákazníkům používat jednu identitu napříč celou aplikace organizace a přístupu pro cloud. Služba Azure Blockchain se integruje s Azure AD umožňuje ID federace, ověření jednotného přihlašování a službou Multi-Factor Authentication. Můžete přiřadit uživatele, skupiny nebo aplikačních rolí ve vaší organizaci pro blockchain přístup člena a uzel.

Proxy klienta služby Azure AD je k dispozici na [Githubu](https://github.com/Microsoft/azure-blockchain-connector/releases). Klient proxy uživatele přesměruje na přihlašovací stránce služby Azure AD a získá nosného tokenu po úspěšném ověření. Následně uživatel připojí aplikaci Etherea klienta, jako je třeba Geth nebo Truffle ke koncovému bodu proxy serveru klienta. Nakonec při odeslání transakce se vkládá nosný token v hlavičce protokolu http proxy serveru klienta a reverzní proxy server ověří token pomocí protokolu OAuth.

## <a name="keys-and-ethereum-accounts"></a>Klíče a Etherea účty

Při zřizování na služby Azure Blockchain člena, je generována účet Ethereem a pár veřejného a privátního klíče. Privátní klíč se používá k odeslání transakce blockchainu. Je účet ethereum během posledních 20 bajtů hash veřejného klíče. Účet ethereum během se také nazývá kapesní.

Pár soukromých a veřejných klíčů se ukládá jako keyfile ve formátu JSON. Privátní klíč je šifrován pomocí hesla zadá, když se vytvoří služba blockchain účetní knihy.

Privátní klíče se používají k digitálnímu podepisování transakce. Inteligentní smlouvy, podepsaný privátním klíčem v privátní blockchainy, představuje totožnost podepsaného. K ověření platnosti podpisu, můžete porovnat příjemce veřejný klíč podpisu s adresou ze signatury vypočítané.

Constellation klíče se používají k jedinečné identifikaci kvora uzlu. Constellation klíče se vygeneruje při zřizování uzlu a jsou určené v parametru privateFor privátní transakce v kvora.

## <a name="next-steps"></a>Další postup

[Konfigurace uzlů transakce služby Azure Blockchain](configure-transaction-nodes.md)
