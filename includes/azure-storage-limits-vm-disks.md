Virtuální počítače Azure podporují připojení řady datových disků. Tento článek popisuje, škálovatelnosti a cílech výkonnosti pro datové disky Virtuálního počítače. Použijte tyto cíle se můžete rozhodnout, počet a typ disku, který je potřeba splnění požadavků na výkon a kapacitu. 

> [!IMPORTANT]
> Pro optimální výkon omezte počet vysoce využívané disky připojené k virtuálnímu počítači, aby se zabránilo možné omezení. Pokud všechny připojené disky nejsou použity vysoce ve stejnou dobu, virtuální počítač může podporovat větší počet disků.

* **Azure spravované disky:** 

> | Prostředek | Výchozí omezení | Maximální omezení |
> | --- | --- | --- |
> | Standard Managed Disks | 10 000 | 50,000 |
> | Standard SSD Managed Disks | 10 000 | 50,000 |
> | Premium Managed Disks | 10 000 | 50,000 |
> | Standard_LRS snímky | 10 000 | 50,000 |
> | Standard_ZRS snímky | 10 000 | 50,000 |
> | Premium_LRS snímky | 10 000 | 50,000 |
> | Spravované bitové kopie | 10 000 | 50,000 |

* **Pro účty úložiště úrovně Standard:** Účet úložiště úrovně Standard má maximální celkovou frekvenci požadavků 20 000 IOPS. Celkový počet IOPS na všech discích virtuálních počítačů v účtu úložiště úrovně Standard by neměl překročit toto omezení.
  
    Na základě omezení frekvence požadavků si můžete přibližně spočítat, kolik vysoce využívaných disků podporuje jeden účet úložiště úrovně Standard. Například pro základní virtuální počítač vrstvy, maximální počet vysoce využívané disků je o 66 (20 000/300 IOPS na disk) a pro standardní vrstvy virtuálního počítače, je o 40 (20 000/500 IOPS na disk). 

* 
  **Pro účty služby Premium Storage:** Účet služby Premium Storage má maximální propustnost 50 Gb/s. Celková propustnost všech disků virtuálních počítačů by neměla překročit toto omezení.

