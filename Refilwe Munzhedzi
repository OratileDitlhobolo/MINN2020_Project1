
"""
PART 3: ANALYSIS & AUTHENTICATION SIM (SIMPLIFIED)

Responsibilities:
 - Provide core analysis functions: aggregation, trend summaries, top-k exports.
 - Provide a tiny authentication simulation to show role-based branching (no real security).
 - Independent: generates sample data if missing (same style as part2).
"""

import os
import pandas as pd
import numpy as np

DATA_FOLDER = "minn_data"
os.makedirs(DATA_FOLDER, exist_ok=True)

PROD_CSV = os.path.join(DATA_FOLDER, "production_stats.csv")
COUNTRIES_CSV = os.path.join(DATA_FOLDER, "countries.csv")
MINERALS_CSV = os.path.join(DATA_FOLDER, "minerals.csv")
USERS_CSV = os.path.join(DATA_FOLDER, "users.csv")

def ensure_sample_prod():
    if not os.path.isfile(PROD_CSV):
        print("[Analysis] production_stats.csv missing — generating lightweight sample.")
        countries = [1,2,3]
        minerals = [1,2]
        rows = []
        for c in countries:
            for m in minerals:
                for y in range(2018, 2024):
                    rows.append({
                        "CountryID": c,
                        "MineralID": m,
                        "Year": y,
                        "Production_tonnes": int(abs(np.random.normal(50000*c*m, 10000))),
                        "ExportValue_BillionUSD": round(abs(np.random.normal(1.0*c*m, 0.2)),2)
                    })
        pd.DataFrame(rows).to_csv(PROD_CSV, index=False)
        print("[Analysis] Sample production_stats.csv written.")

def load_data():
    ensure_sample_prod()
    prod = pd.read_csv(PROD_CSV)
    countries = pd.read_csv(COUNTRIES_CSV) if os.path.isfile(COUNTRIES_CSV) else pd.DataFrame({"CountryID":[1,2,3],"CountryName":["C1","C2","C3"]})
    minerals = pd.read_csv(MINERALS_CSV) if os.path.isfile(MINERALS_CSV) else pd.DataFrame({"MineralID":[1,2],"MineralName":["M1","M2"]})
    return prod, countries, minerals

def aggregate_production(prod_df):
    yearly = prod_df.groupby("Year")["Production_tonnes"].sum().reset_index().sort_values("Year")
    pair = prod_df.groupby(["CountryID","MineralID"])["Production_tonnes"].sum().reset_index().sort_values("Production_tonnes", ascending=False)
    return yearly, pair

def top_countries_by_export(prod_df, countries_df, k=5):
    by_country = prod_df.groupby("CountryID")["ExportValue_BillionUSD"].sum().reset_index()
    by_country = by_country.merge(countries_df, on="CountryID", how="left")
    return by_country.sort_values("ExportValue_BillionUSD", ascending=False).head(k)

def simulate_login(username, password):
    if os.path.isfile(USERS_CSV):
        users = pd.read_csv(USERS_CSV)
        match = users[(users["Username"]==username) & (users["PasswordHash"]==password)]
        if not match.empty:
            roleid = int(match.iloc[0].get("RoleID", 2))
            print(f"[Auth] Login success. RoleID={roleid}")
            return True, roleid
    print("[Auth] Login failed (simulated).")
    return False, None

if __name__ == "__main__":
    prod_df, countries_df, minerals_df = load_data()
    yearly, pairs = aggregate_production(prod_df)
    print("[Analysis] Annual production (sample):")
    print(yearly.tail())
    print("\n[Analysis] Top (CountryID, MineralID) production pairs (sample):")
    print(pairs.head())
    top_export = top_countries_by_export(prod_df, countries_df, k=3)
    print("\n[Analysis] Top countries by export value (sample):")
    print(top_export)
    ok, role = simulate_login("alice", "pass1")
    if ok and role in [1]:
        print("[Analysis] Role 1 (admin) - can run full analysis.")
    else:
        print("[Analysis] Non-admin or failed login - limited outputs.")
