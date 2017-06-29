# Net Interest Margin Classification

### Business Problem
The problem is the rquirement of classifying branches according to their gains in NIM between the given dates
## Step 1: Access data
Below is the query for accessing the required data table. Table names and the 'B' marked branches (which are previously selected by another department) are masked for security issues.

```sql
select 
	t1.ORG_ID, tsb.SUBE_ADI, 
	case when tsb.SUBE_KODU in (
		0,0,0
	) then 'B' else ' ' end as B,
	sum(case when tdkpi.NR_KPI_ID=1000009 then t1.GERCEKLESEN_PAY else 0 end) as TLVadesiz_201705, 
	sum(case when tdkpi.NR_KPI_ID=1000009 then t1.GERCEKLESEN_PAY - t1.ONCEKI_DONEM_PAY2 else 0 end) as TLVadesiz_Gelisim,
	sum(case when tdkpi.NR_KPI_ID=1000013 then t1.GERCEKLESEN_PAY else 0 end) as TLVadeli_201705, 
	sum(case when tdkpi.NR_KPI_ID=1000013 then t1.GERCEKLESEN_PAY - t1.ONCEKI_DONEM_PAY2 else 0 end) as TLVadeli_Gelisim,
	sum(case when tdkpi.NR_KPI_ID=1000026 then t1.GERCEKLESEN_PAY else 0 end) as TLNakdi_201705, 
	sum(case when tdkpi.NR_KPI_ID=1000026 then t1.GERCEKLESEN_PAY - t1.ONCEKI_DONEM_PAY2 else 0 end) as TLNakdi_Gelisim,

	--t1.TARIH, t1.SEVIYE_ID, t1.MIY_SEGMENT_KODU, t1.FREKANS_ID, 
	sum(case when tdkpi.NR_KPI_ID=1000337 then t1.GERCEKLESEN_PAY else 0 end) as NetRand_201705, 
	--sum(case when tdkpi.NR_KPI_ID=1000337 then t1.ONCEKI_DONEM_PAY2 else 0 end) as TL_Net_Rand_201612, 
	sum(case when tdkpi.NR_KPI_ID=1000337 then t1.GERCEKLESEN_PAY - t1.ONCEKI_DONEM_PAY2 else 0 end) as NetRand_Gelisim,

	sum(case when tdkpi.NR_KPI_ID=1000618 then t1.GERCEKLESEN_PAY else 0 end) as NetRandV_201705, 
	--sum(case when tdkpi.NR_KPI_ID=1000618 then t1.ONCEKI_DONEM_PAY2 else 0 end) as TL_Net_Rand_Vdsz_201612, 
	sum(case when tdkpi.NR_KPI_ID=1000618 then t1.GERCEKLESEN_PAY - t1.ONCEKI_DONEM_PAY2 else 0 end) as NetRandV_Gelisim,

	sum(case when tdkpi.NR_KPI_ID=1000002 then t1.GERCEKLESEN_PAY else 0 end) as Net_Faiz_Geliri_201705, 
	--sum(case when tdkpi.NR_KPI_ID=1000002 then t1.ONCEKI_DONEM_PAY2 else 0 end) as Net_Faiz_Geliri_201612, 
	sum(case when tdkpi.NR_KPI_ID=1000002 then t1.GERCEKLESEN_PAY - t1.ONCEKI_DONEM_PAY2 else 0 end) as Net_Faiz_Geliri_Gelisim

from PXXX.XXX_TGNR_ORG t1
left join PXXX.XXX_TKPI_TANIM t2 on t1.KPI_ID=t2.KPI_ID and t1.BAGLI_URUN_ID=t2.BAGLI_URUN_ID and t1.DEGER_TIPI=T2.DEGER_TIPI and t1.PARA_BIRIMI=T2.PARA_BIRIMI
left join PXXX.XXX_KPI_LOOKUP tdkpi on t1.KPI_ID=tdkpi.GNR_KPI_ID and t1.BAGLI_URUN_ID=tdkpi.GNR_BAGLI_URUN_ID
left join PXXX.XXX_TKANAL_SUBE tsb on t1.ORG_ID=tsb.SUBE_KODU
where 
	--t1.ORG_ID in (1328,1431,1206)
	t1.SEVIYE_ID=6
	and t1.TARIH='31.05.2017'
	and tdkpi.NR_KPI_ID in (1000337,1000618,1000002, 1000009, 1000013, 1000026)
	and t1.MBB_SEGMENT=-777
	and t1.MIY_SEGMENT_KODU = -777
	and t1.FREKANS_ID in (2)
	and t1.ORG_ID >0
	and t1.ORG_ID not in (1001,805,1111,622,637,1234, 649)
group by
	t1.TARIH, t1.SEVIYE_ID, t1.ORG_ID, tsb.SUBE_ADI, t1.MIY_SEGMENT_KODU, t1.FREKANS_ID,
	case when tsb.SUBE_KODU in (
		0,0,0
	) then 'B' else ' ' end
order by Net_Faiz_Geliri_Gelisim desc
```

Step 2: Data Preprocessing
