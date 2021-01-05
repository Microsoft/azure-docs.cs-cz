---
title: IT Service Management Connector – zabezpečený export v Azure Monitor – konfigurace Azure
description: V tomto článku se dozvíte, jak nakonfigurovat Azure za účelem připojení ITSM produktů nebo služeb k zabezpečenému exportu v Azure Monitor k centrálnímu monitorování a správě pracovních položek ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 5eb58c48acc7974a4379cf1993a73228c99f5e6d
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857569"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Konfigurace Azure pro připojení nástrojů ITSM pomocí zabezpečeného exportu

Tento článek poskytuje informace o tom, jak nakonfigurovat Azure tak, aby používal "zabezpečený export".
Chcete-li použít "zabezpečený export", postupujte následovně:

1. [Zaregistrovat aplikaci v Azure AD](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Definujte instanční objekt.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Vytvořte zabezpečenou skupinu akcí Webhooku.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Nakonfigurujte své partnerské prostředí.
    Zabezpečený export podporuje připojení pomocí následujících ITSM nástrojů:
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [Helix BMC](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Zaregistrovat s Azure Active Directory

Pomocí těchto kroků zaregistrujete aplikaci do služby Azure AD:

1. Postupujte podle kroků v části [Registrace aplikace s platformou Microsoft Identity](../../active-directory/develop/quickstart-register-app.md).
2. V Azure AD vyberte **zveřejnit aplikaci**.
3. Vyberte **sadu** pro **identifikátor URI ID aplikace**.

   [![Snímek obrazovky s možností nastavení U R I aplikace I D](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. Vyberte **Uložit**.

## <a name="define-service-principal"></a>Definovat instanční objekt

Služba skupiny akcí bude potřebovat oprávnění k získání ověřovacích tokenů z vaší aplikace AAD, aby bylo možné nyní ověřit službu. Chcete-li udělit tato oprávnění, budete muset vytvořit instanční objekt služby pro skupinu akcí ve vašem tenantovi.
K tomuto účelu můžete použít tento [příkaz PowerShellu](./action-groups.md#secure-webhook-powershell-script) . (Vyžaduje oprávnění správce klienta).
V případě volitelného kroku můžete definovat aplikační roli v manifestu vytvořeného aplikace, což vám umožní lépe omezit přístup tak, aby se zprávy mohly posílat jenom některé aplikace s touto konkrétní rolí. Tato role musí být přiřazená k instančnímu objektu skupiny akcí. \
Tento krok lze provést pomocí stejných [příkazů PowerShellu](./action-groups.md#secure-webhook-powershell-script).

## <a name="create-a-secure-webhook-action-group"></a>Vytvoří zabezpečenou skupinu akcí Webhooku.

Po registraci vaší aplikace ve službě Azure AD můžete vytvářet pracovní položky v nástroji ITSM založené na výstrahách Azure pomocí akce zabezpečený Webhook ve skupinách akcí.

Skupiny akcí poskytují modulární a opakovaně použitelný způsob aktivace akcí pro výstrahy Azure. V Azure Portal můžete použít skupiny akcí s upozorněními na metriky, výstrahy protokolu aktivit a výstrahy Azure Log Analytics.
Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](./action-groups.md).

Pokud chcete přidat Webhook k akci, postupujte podle těchto pokynů pro zabezpečený Webhook:

1. V [Azure Portal](https://portal.azure.com/)vyhledejte a vyberte **monitor**. Podokno **monitorování** slučuje všechna nastavení monitorování a data v jednom zobrazení.
2. Vyberte **výstrahy**  >  **Spravovat akce**.
3. Vyberte [Přidat skupinu akcí](./action-groups.md#create-an-action-group-by-using-the-azure-portal)a vyplňte pole.
4. Do pole **název skupiny akcí** zadejte název a zadejte název do pole **krátký název** . Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.
5. Vyberte **zabezpečený Webhook**.
6. Vyberte tyto podrobnosti:
   1. Vyberte ID objektu Azure Active Directory instance, kterou jste zaregistrovali.
   2. V případě identifikátoru URI vložte adresu URL Webhooku, kterou jste zkopírovali z [prostředí nástroje ITSM](#configure-the-itsm-tool-environment).
   3. Nastavte **možnost Povolit společné schéma výstrah** na **Ano**. 

   Následující obrázek ukazuje konfiguraci ukázkové zabezpečené akce Webhooku:

   ![Snímek obrazovky, který zobrazuje zabezpečenou akci Webhooku.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Konfigurace prostředí nástroje ITSM

Konfigurace obsahuje dva kroky:

1. Získejte identifikátor URI pro definici zabezpečeného exportu.
2. Definice podle toku nástroje ITSM

## <a name="next-steps"></a>Další kroky

* [Konfigurace zabezpečeného exportu ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
* [Konfigurace zabezpečeného exportu řadiče pro správu základní desky](./itsmc-secure-webhook-connections-bmc.md)
