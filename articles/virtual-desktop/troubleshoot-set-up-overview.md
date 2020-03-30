---
title: Přehled řešení potíží s Virtuální plochou Windows – Azure
description: Přehled řešení problémů při nastavování prostředí klienta virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127403"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Přehled řešení potíží, zpětná vazba a podpora

Tento článek obsahuje přehled problémů, se kterými se můžete setkat při nastavování prostředí klienta Virtuální plochy systému Windows, a poskytuje způsoby řešení problémů.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu virtuálníplochy windows a](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) prodiskutujte službu Windows Virtual Desktop s produktovým týmem a aktivními členy komunity.

## <a name="escalation-tracks"></a>Eskalace stopy

V následující tabulce můžete identifikovat a vyřešit problémy, se kterými se můžete setkat při nastavování prostředí klienta pomocí klienta vzdálené plochy. Po nastavení vašeho tenanta můžete použít naši novou [službu Diagnostika](diagnostics-role-service.md) k identifikaci problémů pro běžné scénáře.

>[!NOTE]
> Máme fórum technické komunity, které můžete navštívit a probrat své problémy s produktovým týmem a aktivními členy komunity. Navštivte technickou [komunitu virtuálních desktopů windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) a zahajte diskusi.

| **Problém**                                                            | **Navrhované řešení**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Vytvoření klienta virtuální plochy Windows                                                    | Pokud dojde k výpadku Azure, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/); jinak [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte pro službu virtuální plochu **Windows,** vyberte **Nasazení** pro typ problému a pak vyberte **Problémy s vytvořením klienta virtuální plochy Windows** pro podtyp problému.|
| Přístup k šablonám Marketplace na webu Azure Portal       | Pokud dojde k výpadku Azure, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Šablony Virtuální plochy Azure Marketplace jsou volně dostupné.|
| Přístup k šablonám Azure Resource Manageru z GitHubu                                  | Viz část [Vytvoření hostitelských virtuálních počítačů relací virtuálních počítačů virtuálních](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) [počítačů virtuálních počítačů virtuálních počítačů klienta a fondu hostitelů v části](troubleshoot-set-up-issues.md)Vytváření virtuálních počítačů virtuální plochy windows. Pokud problém stále není vyřešen, obraťte se na [tým podpory GitHub](https://github.com/contact). <br> <br> Pokud k chybě dojde po přístupu k šabloně v GitHubu, obraťte se na [podporu Azure](https://azure.microsoft.com/support/create-ticket/).|
| Nastavení fondu hostitelů relací Azure Virtual Network (VNET) a Express Route               | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/)a vyberte příslušnou službu (v kategorii Sítě). |
| Vytvoření virtuálního počítače fondu hostitelů relací, když se nepoužívají šablony Azure Resource Manageru dodávané s Virtuální plochou Windows | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/)a vyberte Virtuální počítač se systémem **Windows** pro službu. <br> <br> Problémy se šablonami Azure Resource Manageru, které jsou součástí Windows Virtual Desktop, najdete v tématu Vytvoření klienta virtuální plochy Windows Virtual Desktop v [části Vytvoření klienta a fondu hostitelů](troubleshoot-set-up-issues.md). |
| Správa hostitelského prostředí relací Virtuální plochy Windows z portálu Azure    | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Problémy se správou při použití prostředí PowerShell se službou Vzdálená plocha/Virtuální plocha Windows najdete v [tématu Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) nebo [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte pro službu virtuální plochu **Windows,** vyberte **Konfigurace a správa** pro typ problému a pak vyberte **Problémy s konfigurací tenanta pomocí powershellu** pro podtyp problému. |
| Správa konfigurace virtuální plochy Windows svázaná s fondy hostitelů a skupinami aplikací (skupiny aplikací)      | Viz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)nebo [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte Windows Virtual **Desktop** pro službu a vyberte příslušný typ problému.|
| Nasazení a správa kontejnerů profilů FSLogix | Viz [Poradce při potížích s produkty FSLogix](/fslogix/fslogix-trouble-shooting-ht/) a pokud se tím problém nevyřeší, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte pro službu virtuální plochu **Windows,** vyberte **fslogix** pro typ problému a vyberte příslušný podtyp problému. |
| Selhání klientů vzdálené plochy při spuštění                                                 | Viz [Poradce při potížích s klientem vzdálené plochy](troubleshoot-client.md) a pokud se tím problém nevyřeší, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte pro službu virtuální plochu **Windows** a pak pro typ problému vyberte **klienty vzdálené plochy.**  <br> <br> Pokud se jedná o problém se sítí, musí uživatelé kontaktovat správce sítě. |
| Připojeno, ale bez zdroje                                                                 | Poradce při potížích s použitím [připojení uživatele se připojuje, ale nic se nezobrazuje (bez zdroje)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) části [připojení služby Windows Virtual Desktop](troubleshoot-service-connection.md)service . <br> <br> Pokud byli uživatelé přiřazeni ke skupině aplikací, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte pro službu virtuální plochu **Windows** a pak pro typ problému vyberte Klienti **vzdálené plochy.** |
| Problémy s zjišťováním kanálu v důsledku sítě                                            | Uživatelé musí kontaktovat správce sítě. |
| Připojení klientů                                                                    | Přečtěte si [téma Připojení služby Windows Virtual Desktop](troubleshoot-service-connection.md) a pokud to problém nevyřeší, [přečtěte si téma Konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md). |
| Odezva vzdálených aplikací nebo plochy                                      | Pokud jsou problémy vázány na konkrétní aplikaci nebo produkt, obraťte se na tým odpovědný za tento produkt. |
| Licenční zprávy nebo chyby                                                          | Pokud jsou problémy vázány na konkrétní aplikaci nebo produkt, obraťte se na tým odpovědný za tento produkt. |
| Problémy při používání nástrojů Virtuální desktop Windows na GitHubu (šablony Azure Resource Manager, diagnostický nástroj, nástroj pro správu) | Problémy najdete [v tématu Šablony Správce prostředků Azure pro vzdálenou plochu.](https://github.com/Azure/RDS-Templates/blob/master/README.md) |

## <a name="next-steps"></a>Další kroky

- Informace o řešení problémů při vytváření fondu klientů a hostitelů v prostředí Virtuální plochy systému Windows najdete v [tématu Vytvoření klientského a hostitelského fondu](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače (VM) ve Windows Virtual Desktop najdete v [tématu Konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Informace o řešení problémů s připojením klienta Virtuální plocha systému Windows naleznete v [tématu Připojení služby Windows Virtual Desktop](troubleshoot-service-connection.md).
- Informace o řešení problémů s klienty vzdálené plochy naleznete [v tématu Poradce při potížích s klientem Vzdálené plochy.](troubleshoot-client.md)
- Informace o řešení problémů při používání PowerShellu s Windows Virtual Desktop najdete v [tématu Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Další informace o službě naleznete v tématu [Windows Virtual Desktop environment](environment-setup.md).
- Chcete-li projít kurz řešení potíží, [přečtěte si článek Návod k řešení potíží s nasazením šablon Správce prostředků](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o auditování akcí naleznete v [tématu Audit operace se Správcem prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Informace o akcích k určení chyb během nasazení najdete v [tématu Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
