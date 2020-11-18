---
title: Nastavení klíčů spravovaných zákazníkem v ověřovacích klíčích Azure | Microsoft Docs
description: Přečtěte si, jak nastavit klíče spravované zákazníkem (CMK) ve službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: bd85936c86656a8ae59a04ccdf53a62bd852368f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655268"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Nastavení klíče spravovaného zákazníkem Sentinel Azure

Tento článek popisuje základní informace a kroky ke konfiguraci klíče spravovaného zákazníkem (CMK) pro Sentinel Azure. CMK umožňuje šifrovat všechna data uložená nebo odesílaná do Azure Sentinel, aby byla zašifrovaná v rámci všech relevantních prostředků úložiště pomocí Azure Key Vaultho klíče vytvořeného nebo vlastněného vámi.

> [!NOTE]
> - Funkce Azure Sentinel CMK je k dispozici pouze **novým zákazníkům**.
>
> - Přístup k této funkci závisí na registraci funkcí Azure. Můžete požádat o přístup kontaktováním azuresentinelCMK@microsoft.com . Nedokončené žádosti budou schváleny na základě dostupné kapacity.
>
> - Funkce CMK je dostupná jenom pro zákazníky, kteří odesílají 1 TB za den. Při použití Microsoftu k zřízení CMK ve vašem předplatném Azure budete dostávat informace o dalších cenách. Přečtěte si další informace o [cenách Log Analytics](../azure-monitor/platform/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="how-cmk-works"></a>Jak funguje CMK 

Řešení Sentinel Azure využívá několik prostředků úložiště pro shromažďování a funkce protokolů, včetně Log Analytics a dalších. V rámci konfigurace Azure Sentinel CMK budete muset taky nakonfigurovat nastavení CMK na souvisejících prostředcích úložiště. Data uložená v jiných úložných zdrojích než Log Analytics budou také šifrována.

Přečtěte si další informace o [CMK](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-overview).

> [!NOTE]
> Pokud povolíte CMK v Azure Sentinel, nebude povolena žádná funkce Public Preview, která nepodporuje CMK.

## <a name="enable-cmk"></a>Povolit CMK 

Při zřizování CMK postupujte takto: 

1.  Vytvoří Azure Key Vault a uloží klíč.

2.  Povolte CMK v pracovním prostoru Log Analytics.

3.  Zaregistrujte se Cosmos DB.

4.  Přidejte zásady přístupu do instance Azure Key Vault.

5.  Povolí CMK ve službě Azure Sentinel.

6.  Povolit Azure Sentinel

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>Krok 1: vytvoření Azure Key Vault a uložení klíče

1.  [Vytvořte prostředek Azure Key Vault](/azure-stack/user/azure-stack-key-vault-manage-portal)a pak vygenerujte nebo importujte klíč, který se použije k šifrování dat.
    > [!NOTE]
    >  Aby bylo možné klíč a přístup chránit, Azure Key Vault musí být nakonfigurovány jako obnovitelné.

1.  [Zapnout možnosti obnovení:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Ujistěte se, že je zapnuté [obnovitelné odstranění](../key-vault/general/soft-delete-overview.md) .

    -   Zapněte funkci [vyprázdnění ochrany](../key-vault/general/soft-delete-overview.md#purge-protection) pro ochranu proti vynucenému odstranění tajného nebo trezoru i po obnovitelném odstranění.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>Krok 2: povolení CMK v pracovním prostoru Log Analytics

Podle pokynů v [Azure monitor konfiguraci klíčů spravovaných zákazníkem](../azure-monitor/platform/customer-managed-keys.md) vytvořte pracovní prostor CMK, který se použije jako pracovní prostor Azure Sentinel v následujících krocích.

### <a name="step-3-register-for-cosmos-db"></a>Krok 3: Zaregistrujte se Cosmos DB

Služba Azure Sentinel funguje s Cosmos DB jako další prostředek úložiště. Ujistěte se, že se zaregistrujete do Cosmos DB.

Pokud chcete zaregistrovat poskytovatele prostředků [Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) pro vaše předplatné Azure, postupujte podle pokynů Cosmos DB.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>Krok 4: Přidání zásady přístupu do instance Azure Key Vault

Nezapomeňte přidat přístup z Cosmos DB do instance Azure Key Vault. Postupujte podle pokynů Cosmos DB a [přidejte do své instance Azure Key Vault zásadu přístupu](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) s Azure Cosmos DBm objektem zabezpečení.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>Krok 5: povolení CMK ve službě Azure Sentinel

Funkce Azure Sentinel CMK je poskytována novým zákazníkům až po přijetí přístupu přímo ze skupiny produktů Azure. Pomocí kontaktů v Microsoftu můžete přijmout schválení od týmu Sentinel Azure a povolit CMK ve vašem řešení.

Po schválení se zobrazí výzva k zadání následujících informací, které umožní funkci CMK povolit.

-  ID pracovního prostoru, na kterém chcete povolit CMK

-  Adresa URL Key Vault: zkopírujte identifikátor klíče klíče "do posledního lomítka:  
    

    ![identifikátor klíče](./media/customer-managed-keys/key-identifier.png)

    Tým Sentinel Azure povolí funkci Azure Sentinel CMK pro váš zadaný pracovní prostor.

-  Ověření z produktového týmu Sentinel Azure, který jste schválili pro použití této funkce. Než budete pokračovat, musíte to mít.

### <a name="step-6-enable-azure-sentinel"></a>Krok 6: povolení služby Azure Sentinel


V pracovním prostoru, na kterém jste nastavili CMK, klikněte na Azure Portal a povolte Azure Sentinel. Další informace najdete v tématu věnovaném [registraci v Azure Sentinel](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Odvolání nebo odstranění šifrovacího klíče


V případě, že uživatel Odvolá šifrovací klíč klíče, a to buď odstraněním nebo odebráním přístupu pro službu Azure Sentinel, během jedné hodiny Azure Sentinele změní a bude fungovat, jako by data už nebudou dostupná. V tomto okamžiku bude znemožněna jakákoli operace, která využívá trvalé prostředky úložiště, jako je například ingestování dat, trvalé změny konfigurace a vytvoření incidentu. Dříve uložená data nebudou smazána, ale zůstanou nepřístupná. Nepřístupná data se řídí zásadami uchovávání dat a budou se v souladu s těmito zásadami vyprázdnit.

Jediná operace, kterou je možné provést po odvolání nebo odstranění šifrovacího klíče, je odstranění účtu.

Pokud se po odvolání obnoví přístup, Azure Sentinel obnoví přístup k datům během hodiny.

Další informace o tom, jak tento postup funguje v Azure Monitor, najdete v článku [Azure monitor odvolání CMK](../azure-monitor/platform/customer-managed-keys.md#key-revocation).

## <a name="key-encryption-key-rotation"></a>Střídání šifrovacího klíče


Azure Sentinel a Log Analytics podporují rotaci klíčů. Když uživatel provede střídání klíčů v Key Vault, Azure Sentinel podporuje nový klíč během hodiny.

V Key Vault můžete provádět rotaci klíčů vytvořením nové verze klíče:

![střídání klíčů](./media/customer-managed-keys/key-rotation.png)

Předchozí verzi klíče můžete zakázat za 24 hodin nebo po Azure Key Vault protokoly auditu nebudou zobrazovat žádné aktivity, které používají předchozí verzi.

Pokud použijete stejný klíč ve službě Azure Sentinel a v Log Analytics, je nutné provést střídání klíčů. v Log Analytics musíte explicitně aktualizovat prostředek clusteru pomocí nové Azure Key Vault verze klíče. Další informace najdete v tématu [Azure monitor rotace CMK](../azure-monitor/platform/customer-managed-keys.md#key-rotation).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak nastavit klíč spravovaný zákazníkem v rámci Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.