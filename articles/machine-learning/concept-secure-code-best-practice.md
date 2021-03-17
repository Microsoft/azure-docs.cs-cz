---
title: Osvědčené postupy zabezpečeného kódu
titleSuffix: Azure Machine Learning
description: Seznamte se s potenciálními bezpečnostními hrozbami, které mohou existovat při vývoji pro Azure Machine Learning, zmírnění a osvědčené postupy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322980"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Bezpečné osvědčené postupy pro kód pomocí Azure Machine Learning

V Azure Machine Learning můžete do Azure nahrávat soubory a obsah z libovolného zdroje. Obsah ve Jupyter poznámkových blocích nebo skriptech, které načtete, může potenciálně číst data z vašich relací, přistupovat k datům v rámci vaší organizace v Azure nebo spouštět škodlivé procesy vaším jménem.

> [!IMPORTANT]
> Spouštějte pouze poznámkové bloky nebo skripty z důvěryhodných zdrojů. Například pokud jste vy nebo váš tým zabezpečení zkontrolovali Poznámkový blok nebo skript.

## <a name="potential-threats"></a>Potenciální hrozby

Vývoj s Azure Machine Learning často zahrnuje webová vývojová prostředí (poznámkové bloky & Azure ML Studio). Při používání webového prostředí pro vývoj jsou možné hrozby:

* [Skriptování mezi weby (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Vkládání DOM__ : Tento typ útoku může změnit uživatelské rozhraní zobrazené v prohlížeči. Například změnou způsobu, jakým se v Jupyter Notebook chová tlačítko Spustit.
    * __Přístup k tokenům a souborům cookie__ : útoky XSS mají přístup také k souborům cookie místního úložiště a prohlížeče. Ověřovací token služby Azure Active Directory (AAD) je uložený v místním úložišti. Útok XSS by mohl tento token použít k tomu, aby vaším jménem volal rozhraní API, a pak data pošle do externího systému nebo rozhraní API.

* [Padělání žádostí mezi weby (CSRF)](https://owasp.org/www-community/attacks/csrf): Tento útok může nahradit adresu URL obrázku nebo odkazem na adresu URL škodlivého skriptu nebo rozhraní API. Při načtení obrázku nebo kliknutí na odkaz se na adresu URL přivede volání.

## <a name="azure-ml-studio-notebooks"></a>Poznámkové bloky Azure ML Studio

Azure Machine Learning Studio poskytuje v prohlížeči hostované prostředí poznámkového bloku. Buňky v poznámkovém bloku můžou vymezit výstup dokumentů nebo fragmentů kódu HTML, které obsahují škodlivý kód.  Když je výstup vykreslen, lze spustit kód.

__Možné hrozby__ :
* Skriptování mezi weby (XSS)
* Padělání žádostí mezi weby (CSRF)

__Omezení rizik, která poskytuje Azure Machine Learning__ :
* __Výstup buňky kódu__ je v izolovaném prostoru (sandbox) v objektu IFRAME. Element IFrame zabrání skriptu v přístupu k nadřazenému modelu DOM, souborům cookie a relaci úložiště.
* Obsah __buňky Markdownu__ se čistí pomocí knihovny dompurify. Tím se zablokuje spouštění škodlivých skriptů s Markdownu buňkami.
* __Adresa URL obrázku__ a __odkazy Markdownu__ se odesílají na koncový bod vlastněný společností Microsoft, který kontroluje škodlivé hodnoty. Pokud je zjištěna škodlivá hodnota, koncový bod požadavek odmítne.

__Doporučené akce__ :
* Před nahráním do studia ověřte, že obsah souborů důvěřujete. Při nahrávání musíte potvrdit, že odesíláte důvěryhodné soubory.
* Když vyberete odkaz pro otevření externí aplikace, zobrazí se výzva k důvěřování aplikace.

## <a name="azure-ml-compute-instance"></a>Instance služby Azure ML COMPUTE

Azure Machine Learning výpočetní instance hostuje __testovací prostředí__ __Jupyter__ a Jupyter. Při použití obou buněk v poznámkovém bloku nebo kódu v aplikaci mohou výstupy dokumentů HTML nebo fragmentů, které obsahují škodlivý kód. Když je výstup vykreslen, lze spustit kód. Stejné hrozby platí i při použití __RStudio__ hostovaného na výpočetní instanci.

__Možné hrozby__ :
* Skriptování mezi weby (XSS)
* Padělání žádostí mezi weby (CSRF)

__Omezení rizik, která poskytuje Azure Machine Learning__ :
* Žádné Jupyter a Jupyter Lab jsou Open Source aplikace hostované na Azure Machine Learning výpočetní instanci.

__Doporučené akce__ :
* Před nahráním do studia ověřte, že obsah souborů důvěřujete. Při nahrávání musíte potvrdit, že odesíláte důvěryhodné soubory.

## <a name="report-security-issues-or-concerns"></a>Nahlásit problémy se zabezpečením nebo obavy 

Azure Machine Learning má nárok v rámci programu Microsoft Azure Bounty. Další informace najdete na adrese  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>Další kroky

* [Podnikové zabezpečení pro Azure Machine Learning](concept-enterprise-security.md)