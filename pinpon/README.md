# ピンポンマンション

公開URL: https://thesakaguchi-lab.github.io/pinpon/

ゲーム本体は `index.html` の1ファイル。設定は冒頭の `CONFIG` だけを書き換える。

## 世界ランキング（Supabase・無料枠）

1. https://supabase.com で無料プロジェクトを作る
2. SQL Editor で下のSQLを実行する
3. Project Settings → API で **Project URL** と **anon public key** をコピーする
4. `index.html` の `CONFIG.SUPABASE_URL` と `CONFIG.SUPABASE_ANON_KEY` に貼る

```sql
create table public.pinpon_scores (
  id bigint generated always as identity primary key,
  name text not null check (char_length(name) between 1 and 12),
  time real not null check (time >= 20 and time <= 600),
  wrong int not null default 0 check (wrong between 0 and 50),
  floor int not null check (floor between 1 and 8),
  created_at timestamptz not null default now()
);
alter table public.pinpon_scores enable row level security;
create policy "anyone can read" on public.pinpon_scores for select using (true);
create policy "anyone can add" on public.pinpon_scores for insert with check (true);
```

anon key はブラウザに出る前提の公開キー。行の読み取りと追加だけを許可し、更新・削除はできない。
ブラウザゲームなのでタイムの偽装は完全には防げない。変な記録は Supabase の Table Editor から消す。

## 広告（AdSense）

`CONFIG.ADSENSE_CLIENT` と `CONFIG.ADSENSE_SLOT` を入れると、タイトル画面と結果画面の「広告枠」に広告が出る。
ただし `github.io` のサブドメインは AdSense の審査に出せないため、広告を出すには独自ドメイン（または審査に通った既存サイト）に置く必要がある。
