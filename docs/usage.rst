========
Usage
========

```python
import numpy as np
from solardatatools.clear_day_detection import find_clear_days
from solardatatools.data_transforms import make_2d
from solardatatools.dataio import get_pvdaq_data

pv_system_data = get_pvdaq_data(sysid=35, api_key='DEMO_KEY', year=[2011, 2012, 2013])

power_signals = make_2d(pv_system_data, key='ac_power')

clear_days = find_clear_days(power_signals)
```
