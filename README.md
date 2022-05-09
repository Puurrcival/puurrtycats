# puurrtycats

import requests, re, tqdm, pandas as pd, json

policy = "f96584c4fcd13cd1702c9be683400072dd1aac853431c99037a3ab1e"
assets = pd.DataFrame(columns=['fingerprint','asset_id','quantity','policy_id','display_name','fur','hat','eyes','mask','tail','hands','mouth','wings','outfit','backround', 'traitcount'])

url=f"""https://server.jpgstoreapis.com/search/tokens?policyIds=[%22{policy}%22]&saleType=default&sortBy=price-low-to-high&traits=%7B%7D&nameQuery=&verified=default&pagination=%7B%7D&size=10000"""
temp = json.loads(requests.get(url).content)

for token in tqdm.tqdm(temp['tokens']):
    try:
        assets.loc[assets.shape[0]]=[token['fingerprint'],token['asset_id'][len(token['policy_id']):],token['quantity'],token['policy_id'], token['display_name']] + [v for k,v in token['traits'].items()]
    except ValueError:
        assets.loc[assets.shape[0]]=[token['fingerprint'],token['asset_id'][len(token['policy_id']):],token['quantity'],token['policy_id'], token['display_name']] + [v for k,v in json.loads(requests.get(f"""https://server.jpgstoreapis.com/token/{token['asset_id']}""").content)['traits'].items()]
temp = None
assets
