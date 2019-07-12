---
title: Řešení potíží s přehled, zpětná vazba a podpora - Azure virtuálního klienta Windows
description: Přehled pro řešení potíží při nastavování prostředí virtuálního klienta Windows tenanta.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b349d7f384c801e95d745f7a18535f9f5d13cf86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605212"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Přehled řešení potíží, zpětná vazba a podpora

Tento článek obsahuje přehled problémy mohou nastat při nastavování prostředí tenanta virtuální plochy Windows a poskytuje způsoby, jak vyřešit problémy.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Můžeme aktuálně nejsou trvá případy podpory virtuální plochy Windows je ve verzi preview. Přejděte [technické komunitě virtuální plochy Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) fattica virtuální plochy Windows service s produktovým týmem a aktivní komunitě členy.

## <a name="escalation-tracks"></a>Eskalace stopy

V následující tabulce můžete identifikovat a řešit problémy, na které můžete narazit při nastavování prostředí tenanta pomocí klienta vzdálené plochy. Váš tenant je nastaveno, můžete použít naši novou [diagnostické služby](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) identifikovat problémy pro běžné scénáře.

>[!NOTE]
>Můžeme aktuálně nejsou trvá případy podpory virtuální plochy Windows je ve verzi preview. Pokaždé, když budeme odkazovat podporu virtuální plochy Windows, přejděte na našem fóru technické komunitě teď. Přejděte [technické komunitě virtuální plochy Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) diskutovat s produktovým týmem a aktivní komunitě členy. Pokud potřebujete podporu problém vyřešit, patří ID aktivity a přibližné časový rámec pro kdy došlo k problému.

| **Problém**                                                            | **Navrhované řešení**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Vytvoření klienta                                                    | Pokud dojde k výpadku Azure, obraťte se na [podpory Azure](https://azure.microsoft.com/support/options/); v opačném případě kontaktní **Podpora vzdálené plochy virtuální služby nebo Windows Desktop**.|
| Přístup k webu Marketplace šablony na webu Azure portal       | Pokud dojde k výpadku Azure, obraťte se na [podpory Azure](https://azure.microsoft.com/support/options/). <br> <br> Šablony Azure Marketplace Windows virtuální plochy jsou volně k dispozici.|
| Přístup k šablon Azure Resource Manageru z Githubu                                  | Najdete v části "Vytvoření Windows Virtual relace plochy hostitele virtuálních počítačů" [Tenanta a hostitele fondu vytváření](troubleshoot-set-up-issues.md). Pokud se jedná o stále nevyřešené, obraťte se [tým podpory Githubu](https://github.com/contact). <br> <br> Pokud po přístupu k šabloně na webu GitHub dojde k chybě, obraťte se na [podpory Azure](https://azure.microsoft.com/support/options/).|
| Nastavení relace hostitele fondu Azure Virtual Network (VNET) a Expressroute               | Kontakt **podpory Azure (síť)** . |
| Relace hostitele fondu vytvoření virtuálního počítače (VM) při šablon Azure Resource Manageru, které jsou součástí virtuálního klienta Windows nejsou používány. | Kontakt **podpory Azure (Compute)** . <br> <br> Problémy s šablonami Azure Resource Manageru, které jsou součástí virtuálního klienta Windows, naleznete v části Vytvoření virtuálního klienta Windows tenanta z [Tenanta a hostitele fondu vytváření](troubleshoot-set-up-issues.md). |
| Správa prostředí hostitele relace Windows virtuální plochy z portálu Azure portal    | Kontakt **podpory Azure**. <br> <br> Potíží se správou při použití vzdálené plochy služby/Windows virtuální plochy Powershellu, najdete v části [Windows Powershellu virtuální plochy](troubleshoot-powershell.md) nebo se obraťte **tým podpory vzdálené plochy virtuální služby nebo Windows Desktop** . |
| Správa konfigurace virtuální plochy Windows spojený s fondy hostitele a skupiny aplikací (skupin aplikací)      | Naleznete v tématu [Windows Powershellu virtuální plochy](troubleshoot-powershell.md), nebo se obraťte **tým podpory vzdálené plochy virtuální služby nebo Windows Desktop**. <br> <br> Pokud problémy jsou svázány se ukázka grafického uživatelského rozhraní (GUI), kontaktujte komunity Yammeru.|
| Klienti služby Vzdálená plocha selhání v nabídce start                                                 | Zobrazit [klienta připojení ke vzdálené ploše](troubleshoot-client-connection.md) a pokud problém nevyřešíte, obraťte se na **tým podpory vzdálené plochy virtuální služby nebo Windows Desktop**.  <br> <br> Pokud se jedná o chybu sítě, budou uživatelé potřebovat kontaktovat svého správce sítě. |
| Připojené, ale žádný kanál                                                                 | Řešení potíží pomocí ", ale nezobrazí se uživatel připojí (žádný kanál)" část [klienta připojení ke vzdálené ploše](troubleshoot-client-connection.md). <br> <br> Pokud vaši uživatelé přiřadily do skupiny aplikaci, odešlete do **tým podpory vzdálené plochy virtuální služby nebo Windows Desktop**. |
| Informační kanál zjišťování potížím způsobeným sítě                                            | Vaši uživatelé muset kontaktovat svého správce sítě. |
| Připojení klientů                                                                    | Zobrazit [připojení klientů vzdálené plochy](troubleshoot-client-connection.md) a pokud váš problém přetrvává, přečtěte si téma [konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md). |
| Rychlost odezvy vzdálené aplikace nebo plochy                                      | Pokud problémy jsou vázané na konkrétní aplikaci nebo produktu, obraťte se na tým odpovědný za konkrétního produktu. |
| Licencování zprávy a chyby                                                          | Pokud problémy jsou vázané na konkrétní aplikaci nebo produktu, obraťte se na tým odpovědný za konkrétního produktu. |

## <a name="next-steps"></a>Další kroky

- Řešení potíží při vytváření fondu tenanta a hostitele v prostředí virtuálního klienta Windows, naleznete v tématu [Tenanta a hostitele fondu vytváření](troubleshoot-set-up-issues.md).
- Řešení potíží při konfiguraci virtuálního počítače (VM) v virtuální plochy Windows, naleznete v tématu [konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Řešení potíží s připojeními klientů virtuální plochy Windows, naleznete v tématu [klienta připojení ke vzdálené ploše](troubleshoot-client-connection.md).
- Při řešení problémů při použití Powershellu s virtuální plochy Windows, naleznete v tématu [Windows Powershellu virtuální plochy](troubleshoot-powershell.md).
- Další informace o služba ve verzi Preview, najdete v článku [prostředí Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Absolvovat kurz řešení potíží, najdete v článku [kurzu: Řešení potíží s nasazení šablon Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Další informace o auditování akcí najdete v tématu [Audit operací pomocí Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích, chcete-li zjistit chyby během nasazení najdete v tématu [zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).