Virtuální počítače Azure podporují připojení řady datových disků. Tento článek popisuje, škálovatelnost a výkonnostní cíle pro datové disky Virtuálního počítače. Pomocí těchto cílů pomáhá v rozhodování, počet a typ disku, který je potřeba zajistit splnění požadavků na výkon a kapacitu. 

> [!IMPORTANT]
> Pro optimální výkon omezte počet vysoce využívaných disků připojených k virtuálnímu počítači, abyste zabránili možnému omezení. Pokud všech připojených discích nejsou využívá vysoce ve stejnou dobu, můžete virtuální počítač podporovat větší počet disků.

* **Azure Managed Disks:** 

> | Prostředek | Výchozí omezení | Maximální omezení |
> | --- | --- | --- |
> | Standard Managed Disks | 10 000 | 50,000 |
> | Standard SSD Managed Disks | 10 000 | 50,000 |
> | Premium Managed Disks | 10 000 | 50,000 |
> | Standard_LRS snímky | 10 000 | 50,000 |
> | Standard_ZRS snímky | 10 000 | 50,000 |
> | Spravované Image | 10 000 | 50,000 |

* **Pro účty úložiště úrovně Standard:** Účet úložiště úrovně Standard má maximální celkovou frekvenci požadavků 20 000 IOPS. Celkový počet IOPS na všech discích virtuálních počítačů v účtu úložiště úrovně Standard by neměl překročit toto omezení.
  
    Na základě omezení frekvence požadavků si můžete přibližně spočítat, kolik vysoce využívaných disků podporuje jeden účet úložiště úrovně Standard. Například pro virtuální počítač úrovně Basic, maximální počet vysoce využívaných disků asi 66 (20 000/300 IOPS na disk) a pro virtuální počítač úrovně Standard, je přibližně 40 (20 000/500 IOPS na disk). 

* **Pro účty služby Premium Storage:** Účet služby Premium Storage má maximální propustnost 50 Gb/s. Celková propustnost všech disků virtuálních počítačů by neměla překročit toto omezení.

