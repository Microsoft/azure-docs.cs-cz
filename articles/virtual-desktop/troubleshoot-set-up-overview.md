---
title: Přehled řešení potíží s virtuálním počítačem s Windows, názory a podpora – Azure
description: Přehled řešení potíží při nastavování prostředí klienta virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: b16d993717529953da1dc31604e6112f53ed7ac9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679441"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Řešení potíží s přehledem, zpětnou vazbou a podporou

Tento článek poskytuje přehled o problémech, se kterými se můžete setkat při nastavování prostředí tenanta virtuálních počítačů s Windows, a poskytuje způsoby, jak tyto problémy vyřešit.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="escalation-tracks"></a>Cvičení eskalace

Pomocí následující tabulky můžete identifikovat a vyřešit problémy, se kterými se můžete setkat při nastavování prostředí klienta pomocí klienta vzdálené plochy. Po nastavení vašeho tenanta můžete využít naši novou [službu diagnostiky](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) k identifikaci problémů pro běžné scénáře.

>[!NOTE]
> Máme Fórum odborné komunity, které můžete navštívit a diskutovat o problémech s produktovým týmem a aktivními členy komunity. Navštivte [technickou komunitu virtuálních počítačů s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) a 

| **Chybu**                                                            | **Navrhované řešení**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Vytváření tenanta                                                    | Pokud dojde k výpadku Azure, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/). jinak **otevřete žádost o podporu pro virtuální počítač s Windows (COMPUTE)** .|
| Přístup k šablonám Marketplace v Azure Portal       | Pokud dojde k výpadku Azure, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/). <br> <br> Azure Marketplace šablony virtuálních klientů Windows jsou volně dostupné.|
| Přístup k šablonám Azure Resource Manager z GitHubu                                  | Přečtěte si část vytvoření virtuálních počítačů hostitele relace virtuálních počítačů s Windows v tématu vytvoření [fondu hostitelů a vytváření fondů hostitelů](troubleshoot-set-up-issues.md). Pokud je problém stále nevyřešený, obraťte se na [tým podpory GitHubu](https://github.com/contact). <br> <br> Pokud k této chybě dojde po přístupu k šabloně v GitHubu, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/).|
| Fond hostitelů relace – Azure Virtual Network (VNET) a nastavení expresní trasy               | Kontaktujte **podporu Azure (sítě)** . |
| Vytvoření virtuálního počítače fondu hostitelů relace, když se nepoužívají šablony Azure Resource Manager s virtuálním počítačem s Windows | Kontaktujte **podporu Azure (COMPUTE)** . <br> <br> Problémy s Azure Resource Manager šablonami, které jsou k dispozici s virtuálním počítačem s Windows, najdete v části Vytvoření tenanta virtuálních počítačů s Windows v tématu vytváření [fondů klientů a fondů hostitelů](troubleshoot-set-up-issues.md). |
| Správa prostředí hostitele relace virtuálních počítačů s Windows z Azure Portal    | Kontaktujte **podporu Azure**. <br> <br> Problémy se správou při používání PowerShellu služby Vzdálená plocha/prostředí PowerShell pro virtuální počítače s Windows najdete v tématu [virtuální plocha Windows PowerShell](troubleshoot-powershell.md) nebo **otevřete žádost o podporu pro virtuální plochu Windows (COMPUTE)** . |
| Správa konfigurace virtuálních klientských počítačů s Windows vázaných na fondy hostitelů a skupiny aplikací (skupiny aplikací)      | Podívejte se na téma [PowerShell virtuální plochy Windows](troubleshoot-powershell.md)nebo **otevřete žádost o podporu pro virtuální plochu Windows (COMPUTE)** . <br> <br> Pokud jsou problémy svázané s ukázkovým grafickým uživatelským rozhraním (GUI), můžete se obrátit na komunitu Yammer.|
| Selhání klientů vzdálené plochy při spuštění                                                 | Podívejte se na [připojení klienta vzdálené plochy](troubleshoot-client-connection.md) . Pokud to problém nevyřeší, **otevřete žádost o podporu pro virtuální plochu Windows (COMPUTE)** .  <br> <br> Pokud se jedná o problém v síti, uživatelé musí kontaktovat svého správce sítě. |
| Připojeno, ale bez kanálu                                                                 | Řešení potíží s použitím "připojovat uživatele", ale nic se nezobrazuje (bez informačního kanálu) oddílu [připojení klienta vzdálené plochy](troubleshoot-client-connection.md). <br> <br> Pokud byli vaši uživatelé přiřazeni ke skupině aplikací, **otevřete žádost o podporu pro virtuální plochu Windows (COMPUTE)** . |
| Podávat potíže se zjišťováním v důsledku sítě                                            | Uživatelé musí kontaktovat svého správce sítě. |
| Připojování klientů                                                                    | Viz [připojení klientů vzdálené plochy](troubleshoot-client-connection.md) a pokud problém nevyřeší, přečtěte si téma [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md). |
| Reakce vzdálených aplikací nebo plochy                                      | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |
| Licenční zprávy a chyby                                                          | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |

## <a name="next-steps"></a>Další kroky

- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení problémů s připojením klienta k virtuální ploše Windows najdete v tématu [připojení klientů vzdálené plochy](troubleshoot-client-connection.md).
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
