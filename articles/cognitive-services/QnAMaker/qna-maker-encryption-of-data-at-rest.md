---
title: QnA Maker šifrování dat v klidovém stavu
titleSuffix: Azure Cognitive Services
description: QnA Maker šifrování dat v klidovém stavu .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372092"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker šifrování dat v klidovém stavu

QnA Maker automaticky šifruje vaše data, když jsou v cloudu trvalá, což pomáhá splnit cíle vaší organizace v oblasti zabezpečení a dodržování předpisů.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Ve výchozím nastavení vaše předplatné používá šifrovací klíče spravované společností Microsoft. K dispozici je také možnost spravovat předplatné pomocí vlastních klíčů. Klíče spravované zákazníkem (CMK) nabízejí větší flexibilitu při vytváření, otáčení, zakázání a odvolání ovládacích prvků přístupu. Můžete také auditovat šifrovací klíče používané k ochraně dat.

QnA Maker používá podporu CMK z Azure search. Potřebujete vytvořit [CMK ve službě Azure Search pomocí azure key vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Tato instance Azure by měla být přidružena ke službě QnA Maker, aby byla povolena CMK.

> [!IMPORTANT]
> Prostředek služby Azure Search musí být vytvořen po lednu 2019 a nemůže být na bezplatné (sdílené) úrovni. Neexistuje žádná podpora konfigurace klíčů spravovaných zákazníky na webu Azure Portal.

## <a name="enable-customer-managed-keys"></a>Povolení klíčů spravovaných zákazníkem

Služba QnA Maker používá CMK ze služby Azure Search. Chcete-li povolit sady CMK, postupujte takto:

1. Vytvořte novou instanci Azure Search a povolte požadavky uvedené v [požadavcích klíče spravovanézákazníkem pro Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Zobrazit nastavení šifrování](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Když vytvoříte prostředek QnA Maker, je automaticky přidružen k instanci Azure Search. To nelze použít s CMK. Chcete-li použít CMK, budete muset přidružit nově vytvořenou instanci Azure Search, která byla vytvořena v kroku 1. Konkrétně budete muset aktualizovat `AzureSearchAdminKey` a `AzureSearchName` ve vašem prostředku QnA Maker.

   ![Zobrazit nastavení šifrování](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Dále vytvořte nové nastavení aplikace:
   * **Název**: Nastavte tuto`CustomerManagedEncryptionKeyUrl`
   * **Hodnota:** Toto je hodnota, kterou jste získali v kroku 1 při vytváření instance Azure Search.

   ![Zobrazit nastavení šifrování](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Po dokončení restartujte za běhu. Nyní je vaše služba QnA Maker povolena cmk.

## <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou dostupné ve všech oblastech Azure Search.

## <a name="next-steps"></a>Další kroky

* [Šifrování ve službě Azure Search pomocí cmk v azure key vaultu](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Šifrování dat v klidovém stavu](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
