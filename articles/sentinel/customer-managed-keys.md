---
title: Nastavení klíčů spravovaných zákazníky v Azure Sentinelu| Dokumenty společnosti Microsoft
description: Zjistěte, jak nastavit klíče spravované zákazníkem (CMK) v Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: bc2fa02925e7f5c671085eb87ca0431d3fca7691
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587953"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Nastavení klíče spravovaného zákazníkem Azure Sentinelu


Tento článek obsahuje základní informace a kroky ke konfiguraci klíče spravovaného zákazníkem (CMK) pro Azure Sentinel. CMK umožňuje, aby všechna data uložená nebo odeslaná do Azure Sentinelu byla šifrována ve všech příslušných prostředcích úložiště pomocí klíče Azure Key Vault, který jste vytvořili nebo vlastnili.

> [!NOTE]
> -   Funkce Azure Sentinel CMK je k dispozici pouze zákazníkům, kteří jsou **nové** a přístup k této funkci je řízen registrací funkcí Azure.Můžete požádat o azuresentinelCMK@microsoft.compřístup kontaktováním a jakmile bude kapacita k dispozici, budou schváleny nevyřízené žádosti.
> -   Funkce Azure Sentinel CMK je k dispozici pouze v oblastech USA – východ, USA – západ a jih – střed.
> -   Funkce CMK je k dispozici pouze zákazníkům, kteří posílají 1 TB za den nebo více. Informace o dalších cenách obdržíte, když požádáte Microsoft o zřízení cmk v rámci předplatného Azure. Přečtěte si další informace o účtování poplatků [službou Log Analytics.](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Jak CMK funguje 

Řešení Azure Sentinel používá několik prostředků úložiště pro shromažďování protokolů a funkce, mezi ně patří Log Analytics a další prostředky úložiště. Jako součást konfigurace Azure Sentinel CMK budete muset nakonfigurovat nastavení CMK na související prostředky úložiště také. Data uložená v jiných prostředkůch úložiště než Log Analytics budou také šifrována.

> [!NOTE]
> Pokud povolíte CMK v Azure Sentinelu, žádná funkce Public Preview, která nepodporuje CMK nebude povolena.

## <a name="enable-cmk"></a>Povolit CMK 

Chcete-li zřídit CMK, postupujte takto: 

1.  Vytvořte trezor klíčů Azure a ukládající klíč.

2.  Povolte CMK v pracovním prostoru Log Analytics.

3.  Zaregistrujte se do Cosmos DB.

4.  Přidejte zásady přístupu k instanci Azure Key Vault.

5.  Povolte CMK v Azure Sentinelu.

6.  Povolte Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>KROK 1: Vytvoření trezoru klíčů Azure a uložení klíče

1.  [Vytvořte prostředek Azure Key Vault](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)a pak vygenerujte nebo importujte klíč, který se použije pro šifrování dat.
    > [!NOTE]
    >  Azure Key Vault musí být nakonfigurovaný jako obnovitelný k ochraně vašeho klíče a přístupu.

1.  [Zapněte možnosti obnovení:](../key-vault/key-vault-best-practices.md#turn-on-recovery-options)

    -   Zkontrolujte, zda je zapnuto [měkké odstranění.](../key-vault/key-vault-ovw-soft-delete.md)

    -   Zapněte [ochranu proti pročištění,](../key-vault/key-vault-ovw-soft-delete.md#purge-protection) abyste se ochránili před vynuceným odstraněním tajného klíče/trezoru i po měkkém odstranění.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>KROK 2: Povolení CMK v pracovním prostoru Log Analytics

Postupujte podle pokynů v [Azure Monitoru konfigurace klíče spravované zákazníkem](../azure-monitor/platform/customer-managed-keys.md) k vytvoření cmk pracovní prostor, který se bude používat jako pracovní prostor Azure Sentinel v následujících krocích.

### <a name="step-3-register-for-cosmos-db"></a>KROK 3: Registrace pro Cosmos DB

Azure Sentinel spolupracuje s Cosmos DB jako další prostředek úložiště. Ujistěte se, že se zaregistrujete do Cosmos DB.

Podle pokynů Cosmos DB zaregistrujte poskytovatele prostředků [Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) pro vaše předplatné Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>KROK 4: Přidání zásad přístupu k instanci azure key vault

Nezapomeňte do instance Azure Key Vault přidat přístup z Cosmos DB. Postupujte podle pokynů Cosmos DB a [přidejte zásady přístupu k instanci Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) s objektem Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>KROK 5: Povolení CMK v Azure Sentinelu

Funkce Azure Sentinel CMK je k dispozici novým zákazníkům pouze po získání přístupu přímo ze skupiny produktů Azure. Použijte své kontakty v Microsoftu k získání schválení od týmu Azure Sentinel umožněte CMK ve vašem řešení.

Po získání schválení budete požádáni o poskytnutí následujících informací, které povolit funkci CMK.

-  ID pracovního prostoru, u kterého chcete povolit CMK

-  Adresa URL trezoru klíčů: Zkopírujte klíč "Identifikátor klíče" až do posledního lomítka:  
    

    ![identifikátor klíče](./media/customer-managed-keys/key-identifier.png)

    Tým Azure Sentinelu povolí funkci Azure Sentinel CMK pro váš poskytnutý pracovní prostor.

-  Ověření od produktového týmu Azure Sentinel, že jste byli schváleni k použití této funkce. Musíte to mít, než budete pokračovat.

### <a name="step-6-enable-azure-sentinel"></a>KROK 6: Povolení Azure Sentinelu


Přejděte na portál Azure a povolte Azure Sentinel v pracovním prostoru, ve kterém nastavíte CMK. Další informace najdete v tématu [Azure Sentinel Boarding](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Odvolání nebo odstranění šifrovacího klíče klíče


V případě, že uživatel odvolá šifrovací klíč klíče, buď jeho odstraněním nebo odebráním přístupu pro Azure Sentinel, do jedné hodiny, Azure Sentinel bude ctít změny a chovat se, jako by data již není k dispozici. V tomto okamžiku bude zabráněno jakékoli provedené operaci, která používá prostředky trvalého úložiště, jako je například ingestování dat, trvalé změny konfigurace a vytváření incidentů. Dříve uložená data nebudou odstraněna, ale zůstanou nedostupná. Nedostupná data se řídí zásadami uchovávání dat a budou vymazána v souladu s těmito zásadami.

Jedinou možnou operací po odvolání nebo odstranění šifrovacího klíče je odstranění účtu.

Pokud se přístup obnoví po odvolání, Azure Sentinel obnoví přístup k datům během jedné hodiny.

Další informace o tom, jak to funguje ve službě Azure Monitor, najdete v [tématu azure monitor uvolne tekce CMK](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation).

## <a name="key-encryption-key-rotation"></a>Otočení šifrovacího klíče klíče


Azure Sentinel a Log Analytics podporují střídání klíčů. Když uživatel provede střídání klíčů v trezoru klíčů, Azure Sentinel podporuje nový klíč během jedné hodiny.

V trezoru klíčů můžete provádět střídání klíčů vytvořením nové verze klíče:

![otočení klíče](./media/customer-managed-keys/key-rotation.png)

Můžete zakázat předchozí verzi klíče po 24 hodinách nebo po protokoly auditu Azure Key Vault již nezobrazují žádnou aktivitu, která používá předchozí verzi.

Pokud používáte stejný klíč v Azure Sentinelu a v Log Analytics, je nutné provést střídání klíčů, musíte explicitně aktualizovat prostředek clusteru v Log Analytics s novou verzí klíče Azure Key Vault. Další informace naleznete v [tématu Azure Monitor CMK rotace](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili, jak nastavit klíč spravovaný zákazníkem v Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)

