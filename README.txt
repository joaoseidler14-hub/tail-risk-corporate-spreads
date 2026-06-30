+=====================================================================+
|      ___  ____  ____    _    ____                                   |
|     / _ \/ ___|| __ )  / \  |  _ \    Open Source                   |
|    | | | \___ \|  _ \ / _ \ | |_) |   Bond Asset Pricing            |
|    | |_| |___) | |_) / ___ \|  __/    Corporate Bond Factor Data    |
|     \___/|____/|____/_/   \_\_|       v2026-0.0.0                   |
+=====================================================================+

                    PUBLIC BETA - Use at Your Own Risk

                    Sample Period: August 2002 - March 2025
                    108 Corporate Bond Factor Signals

================================================================================
                              LEGAL DISCLAIMER
================================================================================

We do NOT distribute proprietary data. The following adjustments apply:

  - gvkey and permco are set to NaN (proprietary identifiers)

  - spc_rat and mdc_rat are AUGMENTED ratings, NOT raw S&P/Moody's ratings:
      * Set to 1  if original rating is 1-10  (Investment Grade)
      * Set to 11 if original rating is 11+   (Non-Investment Grade / Default)

  - If you hold a valid license for credit ratings, merging is trivial via cusip

All returns, signals, and factors in this database are computed by the authors.
No proprietary data from any data provider is distributed.

================================================================================
                              QUICK START
================================================================================

The main panel is ready to use AS-IS. Price-based signals are already
market-microstructure (MMN) adjusted.

    import pandas as pd
    data = pd.read_parquet('main_panel_2025.parquet')

The 108 factor signals begin after the 'sig_gap' column.

================================================================================
                              FILES INCLUDED
================================================================================

Data is distributed in two packages. Download both for full functionality.

  PACKAGE 1: osbap_main_data_2025_public_beta.zip
  -----------------------------------------------------------------------------
  main_panel_2025.parquet                Main panel with MMN-adjusted price signals
                                         (108 factor signals, ready to use)

  PACKAGE 2: osbap_additional_data_2025_public_beta.zip
  -----------------------------------------------------------------------------
  betas_x_2025.parquet                   Factor betas from duration-adjusted returns
  mom_retx_2025.parquet                  Momentum/LTR from duration-adjusted returns
  mmn_price_based_signals_2025.parquet   Unadjusted price signals (*_mmn suffix)
  returns_alt_2025.parquet               Alternative return measures

  NOTE: Package 1 is sufficient for most research designs using excess returns.
        Package 2 is needed for duration-adjusted returns or advanced use cases.

  DOWNLOAD: Package 2 is available at https://openbondassetpricing.com/
            Navigate to: TRACE > Panel Data > Monthly Data > Additional Data

================================================================================
                         HOW TO USE THE DATA
================================================================================

+-----------------------------------------------------------------------------+
|  DECISION 1: What return measure?                                           |
+-----------------------------------------------------------------------------+
|                                                                             |
|  +---------------------+         +--------------------------------------+   |
|  | EXCESS RETURNS      |         | DURATION-ADJUSTED RETURNS            |   |
|  | r - rf              |         | r - tret                             |   |
|  +----------+----------+         +------------------+-------------------+   |
|             |                                       |                       |
|             v                                       v                       |
|  +---------------------+         +--------------------------------------+   |
|  | USE:                |         | USE:                                 |   |
|  |  main_panel_2025    |         |  main_panel_2025                     |   |
|  |                     |         |  + betas_x_2025 (merge on cusip_id,  |   |
|  | COMPUTE:            |         |                   month_year)        |   |
|  |  ret_vw - rfret     |         |  + mom_retx_2025                     |   |
|  |                     |         |                                      |   |
|  | All signals in      |         | COMPUTE:                             |   |
|  | main_panel are      |         |  ret_vw - tret                       |   |
|  | ready to use.       |         |                                      |   |
|  +---------------------+         +--------------------------------------+   |
|                                                                             |
+-----------------------------------------------------------------------------+

+-----------------------------------------------------------------------------+
|  DECISION 2: Month-End or Month-Begin returns?                              |
+-----------------------------------------------------------------------------+
|                                                                             |
|  +-----------------------------+    +-----------------------------------+   |
|  | MONTH-END (ret_vw)          |    | MONTH-BEGIN (ret_vw_bgn)          |   |
|  |                             |    |                                   |   |
|  | Standard approach.          |    | Use when:                         |   |
|  | Use with MMN-adjusted       |    |  - Using UNADJUSTED price signals |   |
|  | signals in main_panel.      |    |    from mmn_price_based_signals   |   |
|  |                             |    |  - Testing implementable returns  |   |
|  | RECOMMENDED for most        |    |                                   |   |
|  | research designs.           |    | Avoids microstructure bias when   |   |
|  +-----------------------------+    | signal price = return start price |   |
|                                     +-----------------------------------+   |
|                                                                             |
|  NOTE: If using mmn_price_based_signals_2025.parquet, you MUST use          |
|        ret_vw_bgn to avoid look-ahead bias.                                 |
|                                                                             |
+-----------------------------------------------------------------------------+

================================================================================
                         KEY VARIABLES
================================================================================

  Variable        Description
  -----------------------------------------------------------------------------
  cusip_id        Bond identifier (CUSIP)
  month_year      Period (monthly frequency)
  ret_vw          Month-end total return (volume-weighted)
  ret_vw_bgn      Month-begin total return
  tret            Duration-matched Treasury return
  rfret           Risk-free rate (Fama-French)
  md_dur          Modified duration
  spc_rat         Augmented S&P rating (1=IG, 11=NIG)
  mdc_rat         Augmented Moody's rating (1=IG, 11=NIG)
  sig_gap         Signal gap (signals begin after this column)

================================================================================
                         EXAMPLE: BASIC FACTOR PORTFOLIO
================================================================================

    import PyBondLab as pbl

    # Create a single-sort strategy
    strategy = pbl.SingleSort(
        holding_period=1,
        sort_var='cs',
        num_portfolios=10
    )

    # Execute the strategy
    sf = pbl.StrategyFormation(data=data, strategy=strategy)

    # Map columns in .fit()
    result = sf.fit(
        IDvar='cusip',           # Maps 'cusip'   -> 'ID'
        RETvar='ret_vw',         # Maps 'ret_vw'  -> 'ret'
        VWvar='mcap_e',          # Maps 'mcap_e'  -> 'VW'
        RATINGvar='spc_rat',     # Maps 'spc_rat' -> 'RATING_NUM'
    )

    # Access results
    ew_ls, vw_ls = result.get_long_short()

================================================================================
                         DOCUMENTATION & RESOURCES
================================================================================

  Full Data Dictionary:
    https://github.com/Alexander-M-Dickerson/trace-data-pipeline/blob/main/stage2/DATA_DICTIONARY.md

  Official Website:
    https://openbondassetpricing.com/

  GitHub Repository:
    https://github.com/Alexander-M-Dickerson/trace-data-pipeline

  Contact:
    alexander.dickerson1@unsw.edu.au

================================================================================
                         CITATION
================================================================================

If you use this data, please cite:

  Dickerson, A., Robotti, C., & Rossetti, G. (2026). The Corporate Bond Factor
  Replication Crisis: A New Protocol. Working Paper.

BibTeX:

  @article{dickerson2026replicationcrisis,
    title={The Corporate Bond Factor Replication Crisis: A New Protocol},
    author={Dickerson, Alexander and Robotti, Cesare and Rossetti, Giulio},
    journal={Working Paper},
    year={2026}
  }

================================================================================
                         LICENSE
================================================================================

This data is provided freely for academic and commercial use. No restrictions.
Attribution via citation is appreciated but not required.

================================================================================
