---
title: Řešení potíží s připojením mezi službou Synapse Studio a úložištěm
description: Řešení potíží s připojením mezi službou Synapse Studio a úložištěm
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117057"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Řešení potíží s připojením mezi funkcí Synapse Studio služby Azure Synapse Analytics a úložištěm

V synapse studiu můžete prozkoumat datové prostředky, které se nacházejí v propojeném úložišti. Tato příručka vám pomůže vyřešit problémy s připojením při pokusu o přístup k datovým prostředkům. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Případ #1: účet úložiště nemá správná oprávnění.

Pokud váš účet úložiště nemá správná oprávnění, nebudete moct rozšířit strukturu úložiště prostřednictvím "dat"--> "propojených" v synapse studiu. Podívejte se na snímek obrazovky s příznakem problému níže. 

Podrobná chybová zpráva se může lišit, ale obecný význam chybové zprávy: "Tato žádost není autorizována k provedení této operace".

V uzlu propojeného úložiště:  
![Potíže s připojením úložiště 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

V uzlu kontejner úložiště:  
![Potíže s připojením úložiště 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**Řešení**: Pokud chcete přiřadit účet ke správné roli, přečtěte si téma [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](../../storage/common/storage-auth-aad-rbac-portal.md) .


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Případ #2: odeslání požadavku na server úložiště se nezdařilo

Když vyberete šipku pro rozšíření struktury úložiště v "data"--> "propojený" v nástroji synapse Studio, může se na levém panelu zobrazit problém "REQUEST_SEND_ERROR". Podívejte se na následující snímek obrazovky s příznakem problému:

V uzlu propojeného úložiště:  
![Potíže s připojením úložiště 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

V uzlu kontejner úložiště:  
![Potíže s připojením úložiště 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Je možné, že tento problém může nastat z několika důvodů:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>Prostředek úložiště je za virtuální sítí a privátní koncový bod úložiště musí být nakonfigurovaný.

**Řešení**: v tomto případě musíte pro svůj účet úložiště nakonfigurovat privátní koncový bod úložiště. Postup konfigurace privátního koncového bodu úložiště pro virtuální síť najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](../security/how-to-connect-to-workspace-from-restricted-network.md).

\<storage-account-name\>Po nakonfigurování privátního koncového bodu úložiště můžete pomocí příkazu "nslookup. DFS.Core.Windows.NET" ověřit připojení. Měl by vracet řetězec podobný řetězci: " \<storage-account-name\> . privatelink.DFS.Core.Windows.NET".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>Prostředek úložiště není za virtuální sítí, ale koncový bod služby Blob service (Azure AD) není dostupný kvůli nakonfigurované bráně firewall.

**Řešení**: v tomto případě je potřeba otevřít účet úložiště v Azure Portal. V levém navigačním panelu přejděte dolů na **support + Troubleshooting** a vyberte **kontrolu připojení** a ověřte stav připojení **BLOB Service (Azure AD)** . Pokud k ní nemáte přístup, postupujte podle pokynů Průvodce povýšením a ověřte konfiguraci **bran firewall a virtuálních sítí** na stránce vašeho účtu úložiště. Další informace o branách firewall úložiště najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>Další problémy ke kontrole 

* Prostředek úložiště, ke kterému přistupujete, je Azure Data Lake Storage Gen2 a za stejnou dobu je za bránou firewall a virtuální síť (s nakonfigurovaným privátním koncovým bodem úložiště).
* Prostředek kontejneru, ke kterému přistupujete, byl odstraněn nebo neexistuje.
* Křížení klienta: tenant pracovního prostoru, který uživatel použil k přihlášení, není stejný jako tenant účtu úložiště. 


## <a name="next-steps"></a>Další kroky
Pokud vám předchozí kroky nepomohly problém vyřešit, [vytvořte lístek podpory](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).