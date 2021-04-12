---
title: Připojení hybridních počítačů k Azure z centra pro správu Windows
description: V tomto článku se dozvíte, jak nainstalovat agenta a připojit počítače k Azure pomocí serverů s podporou ARC Azure z centra pro správu systému Windows.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 5fa7a61b1e3b22503377cbcbe308a82be89dac72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584767"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Připojení hybridních počítačů k Azure z centra pro správu Windows

Servery s podporou ARC Azure můžete povolit pro jeden nebo více počítačů s Windows ve vašem prostředí provedením sady kroků ručně. Nebo můžete použít [Centrum pro správu systému Windows](/windows-server/manage/windows-admin-center/understand/what-is) k nasazení agenta připojeného počítače a registraci místních serverů bez nutnosti provádět žádné kroky mimo tento nástroj.

## <a name="prerequisites"></a>Požadavky

* Servery s podporou ARC – zkontrolujte [požadavky](agent-overview.md#prerequisites) a ověřte, že vaše předplatné, účet Azure a prostředky splňují požadavky.

* Centrum pro správu systému Windows – Projděte si požadavky na [přípravu prostředí](/windows-server/manage/windows-admin-center/deploy/prepare-environment) pro nasazení a [konfiguraci Integrace Azure ](/windows-server/manage/windows-admin-center/azure/azure-integration).

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Cílové servery se systémem Windows, které chcete spravovat, musí mít připojení k Internetu pro přístup k Azure.

### <a name="security"></a>Zabezpečení

Tato metoda nasazení vyžaduje, abyste měli na cílovém počítači nebo serveru s Windows oprávnění správce pro instalaci a konfiguraci agenta. Musíte být také členem role [**Uživatelé brány**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) .

## <a name="deploy"></a>Nasadit

Provedením následujících kroků nakonfigurujte Windows Server se servery s podporou ARC.

1. Přihlaste se do centra pro správu systému Windows.

1. V seznamu připojení na stránce **Přehled** vyberte ze seznamu připojené servery Windows Server, ke kterému se chcete připojit.

1. V levém podokně vyberte **Azure Hybrid Services**.

1. Na stránce **Azure Hybrid Services** vyberte **zjišťování služeb Azure**.

1. Na stránce **Vyhledat služby Azure** v části **využití zásad a řešení Azure ke správě serverů pomocí Azure ARC** vyberte **nastavit**.

1. Pokud se zobrazí výzva k ověření do Azure, na stránce **Settings\Azure ARC pro servery** vyberte **Začínáme.**

1. Na stránce **připojit server k Azure** zadejte následující:

    1. V rozevíracím seznamu **předplatné Azure** vyberte předplatné Azure.
    1. V části **Skupina prostředků** vyberte možnost **nové** , pokud chcete vytvořit novou skupinu prostředků, nebo v rozevíracím seznamu **Skupina prostředků** vyberte existující skupinu prostředků, ze které se má počítač zaregistrovat a spravovat.
    1. V rozevíracím seznamu **oblast** vyberte oblast Azure, do které se budou ukládat metadata serverů.
    1. Pokud počítač nebo server komunikuje prostřednictvím proxy server pro připojení k Internetu, vyberte možnost **použít proxy server**. Zadejte proxy server IP adresu nebo název a číslo portu, které bude počítač používat ke komunikaci s proxy server.

1. Vyberte **nastavit** , abyste mohli pokračovat v konfiguraci Windows serveru se servery s podporou ARC Azure.

Windows Server se připojí k Azure, stáhne agenta připojeného počítače, nainstaluje ho a zaregistruje se na serverech s podporou ARC Azure. Pokud chcete sledovat průběh, v nabídce vyberte **oznámení** .

Chcete-li potvrdit instalaci agenta připojeného počítače, v centru pro správu systému Windows vyberte [**události**](/windows-server/manage/windows-admin-center/use/manage-servers#events) v levém podokně a zkontrolujte události *MsiInstaller* v protokolu událostí aplikace.

## <a name="verify-the-connection-with-azure-arc"></a>Ověření připojení k Azure Arcu

Po instalaci agenta a jeho konfiguraci pro připojení k serverům s podporou ARC Azure klikněte na Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte počítač v [Azure Portal](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Úspěšné připojení k počítači" border="false":::

## <a name="next-steps"></a>Další kroky

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

* Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/vm/vminsights-enable-policy.md)a mnohem víc.

* Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-introduction.md).