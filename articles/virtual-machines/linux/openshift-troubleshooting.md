---
title: Řešení potíží s OpenShift nasazení v Azure | Microsoft Docs
description: Řešení potíží s OpenShift nasazení v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2017
ms.locfileid: "24139446"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Řešení potíží s OpenShift nasazení v Azure

Pokud váš cluster OpenShift není úspěšně nasazena, vyzkoušejte následující řešení potíží s úkoly můžete zúžit problém. Zobrazení stavu nasazení a porovnán v následujícím seznamu ukončovací kód:

- Ukončovací kód 3: vaše Red Hat předplatné uživatelské jméno / heslo nebo ID organizace aktivační klíč je nesprávný
- Ukončovací kód 4: vaše ID fondu Red Hat je nesprávné, nebo nejsou k dispozici žádné oprávnění
- Ukončovací kód 5: nedá zřídit svazek fondu dynamické Docker
- Ukončovací kód 6: OpenShift clusteru instalace se nezdařila.
- Ukončovací kód 7: instalace OpenShift clusteru bylo úspěšné, ale Azure Cloud Solution Provider konfigurace se nezdařila - hlavní konfigurace na potíže hlavní uzel
- Ukončovací kód 8: instalace OpenShift clusteru bylo úspěšné, ale Azure Cloud Solution Provider konfigurace se nezdařila - config uzlu na potíže hlavní uzel
- Ukončovací kód 9: instalace OpenShift clusteru bylo úspěšné, ale Azure Cloud Solution Provider konfigurace se nezdařila - config uzlu na uzel aplikace nebo Infra problém
- Ukončovací kód 10: instalace clusteru OpenShift úspěšně, ale Azure Cloud Solution Provider konfigurace se nezdařila - opravách hlavní uzly nebo nebylo možné nastavit hlavní jako unschedulable
- Ukončovací kód 11: metriky nasazení se nezdařilo.
- Ukončovací kód 12: protokolování nasazení se nezdařilo.

Pro kódy ukončení 7 – 10 OpenShift clusteru byla nainstalována, ale Azure Cloud Solution Provider konfigurace se nezdařilo. Můžete SSH do bastionu uzel (platforma kontejneru OpenShift) nebo hlavní uzel (OpenShift původ) a z tam SSH do každého uzlu clusteru na problémy.

Obvyklou příčinou selhání s kódy ukončení 7 – 9 je, že objekt služby neměl příslušná oprávnění pro předplatné nebo skupinu prostředků. Pokud je problém, přiřadit správná oprávnění a ručně znovu spusťte skript, který se nezdařilo a všechny následné skripty.

Nezapomeňte restartovat službu, která se nezdařila (například systemctl restartování atomic-openshift-node.service) před spuštěním skriptů na znovu.

Pro další řešení potíží s SSH do vaší hlavní uzel na portu 2200 (původ) nebo uzel bastionu na port 22 (platforma kontejneru). Budete muset v kořenovém adresáři (sudo su-) a potom přejděte na následující adresář: /var/lib/waagent/custom-script/download.

Tady vidíte složky s názvem "0" a "1". V každém z těchto složek se zobrazí dva soubory, "stderr" a "stdout." Projděte tyto soubory k určení, kde došlo k chybě.
