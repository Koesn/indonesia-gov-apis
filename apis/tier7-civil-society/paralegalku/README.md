# Paralegalku — Indonesian Regulation Pasal Retriever API

**Provider**: Third-party  
**Base URL**: `https://api.paralegalku.com`  
**Data Source**: `https://peraturan.bpk.go.id`

---

## Overview

Paralegalku is an **Indonesian Legal AI** project focused on providing high-quality legal data for AI-driven legal analysis.

This API provides **high-accuracy pasal-level retrieval** from Indonesian regulations. It returns individual legal provisions/articles that are ready to be used as LLM context, helping make legal analysis more grounded and reducing hallucination risk.

Regulation data is processed from `peraturan.bpk.go.id` and updated **daily**.

Current data status:

- Central Government regulations: **26,917 regulations with 446,128 individual pasal** — ready
- Regional Government regulations: **219,739 regulations** — processing

---

## Community Access

The community tier can be accessed through:

- API access: `https://api.paralegalku.com`
- Web interface: `https://peraturan.paralegalku.com`

---

## Authentication

All `v1` endpoints, except `/health`, require:

```http
Authorization: Bearer <API_KEY>
Content-Type: application/json
```

---

## Rate Limit

Community tier for `peraturan-instant`:

- 1 request/second
- 20 requests/minute
- 200 requests/hour

If the limit is exceeded, the API returns:

```json
{
  "detail": "Rate limit exceeded"
}
```

---

## Endpoint

### Regulation Query

`POST /v1/query/peraturan`

### Request Body

```json
{
  "model": "peraturan-instant",
  "query": "pengusaha dilarang menghambat pembentukan serikat pekerja",
  "top_k": 2
}
```

Fields:

- `model`: `peraturan-instant` or `peraturan-precise`
- `query`: legal search query
- `top_k`: number of results to return (maximum: `20`)

---

## cURL Example

```bash
curl -sS -X POST "https://api.paralegalku.com/v1/query/peraturan" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <API_KEY>" \
  -d '{"model":"peraturan-instant","query":"pengusaha dilarang menghambat pembentukan serikat pekerja","top_k":2}' | jq
```

---

## Response Format

The API returns full pasal and penjelasan content to support grounded legal reasoning and downstream LLM processing.

```json
[
  {
    "regulation_name": "Undang-undang (UU) Nomor 25 Tahun 1997 tentang Ketenagakerjaan",
    "regulation_hierarchy": "Undang-undang (UU)",
    "issuing_authority": "Pemerintah Pusat",
    "validity_status": "Tidak Berlaku",
    "effective_date": "01 Oktober 1998",
    "pasal": [
      {
        "number": "30",
        "content_raw": "\nPasal 30\nPengusaha dilarang menghalang-halangi pekerjanya untuk membentuk dan menjadi pengurus atau anggota serikat pekerja pada perusahaan dan/atau untuk membentuk dan menjadi anggota gabungan serikat pekerja sesuai dengan sektor usaha.\n",
        "penjelasan_raw": "Pasal 30\nTindakan pengusaha yang dapat dianggap menghalang-halangi pekerjanya untuk membentuk dan menjadi pengurus atau anggota serikat pekerja, antara lain:\na. Pengusaha melakukan mutasi terhadap pekerja yang berinisiatif mendirikan serikat pekerja atau gabungan serikat pekerja;\nb. Pengusaha tidak membayar upah kepada pekerja yang melaksanakan kegiatan serikat pekerja atau gabungan serikat pekerja dan telah mendapat izin dari pengusaha;\nc. Pengusaha tidak memberikan kesempatan berupa waktu atau fasilitus bagi pekerja untuk mendirikan serikat pekerja atau gabungan serikat pekerja;\nd. Dengan berbagai dalih, pengusaha melakukan pemutusan hubungan kerja terhadap pengurus serikat pekerja karena melaksanakan tugas-tugas organisasi yang telah diatur dalam peraturan perusahaan atau kesepakatan kerja bersama;\ne. Pengusaha mengadakan kampanye dan tindakan anti pembentukan serikat pekerja atau gabungan serikat pekerja;\nf. Pengusaha mempengaruhi pembentukan dan pemilihan pengurus serikat pekerja atau gabungan serikat pekerja.\n"
      }
    ],
    "score": 0.7175715565681458
  },
  {
    "regulation_name": "Undang-undang (UU) Nomor 21 Tahun 2000 tentang Serikat Pekerja/Serikat Buruh",
    "regulation_hierarchy": "Undang-undang (UU)",
    "issuing_authority": "Pemerintah Pusat",
    "validity_status": "Berlaku",
    "effective_date": "04 Agustus 2000",
    "pasal": [
      {
        "number": "28",
        "content_raw": "\nPasal 28\nSiapapun dilarang menghalang-halangi atau memaksa pekerja/buruh untuk membentuk atau tidak membentuk, menjadi pengurus atau tidak menjadi pengurus, menjadi anggota atau tidak menjadi anggota dan/atau menjalankan atau tidak menjalankan kegiatan serikat pekerja/serikat buruh dengan cara :\na. melakukan pemutusan hubungan kerja, memberhentikan sementara, menurunkan jabatan, atau melakukan mutasi;\nb. tidak membayar atau mengurangi upah pekerja/buruh;\nc. melakukan intimidasi dalam bentuk apapun;\nd. melakukan kampanye anti pembentukan serikat pekerja/serikat buruh.\n",
        "penjelasan_raw": "Pasal 28\nCukup jelas\n"
      }
    ],
    "score": 0.6285747289657593
  }
]
```

---

## Notes

Results from `peraturan-instant` are ranked by semantic similarity, while results from `peraturan-precise` are ranked by substantial relevance. Returned regulations may have different validity statuses, including regulations that are no longer in force. Always check `validity_status` before using the result for legal analysis.

---

## Error Codes

- `400 Bad Request` — invalid payload
- `401 Unauthorized` — missing or invalid API key
- `403 Forbidden` — model not allowed for API key tier
- `429 Too Many Requests` — rate limit exceeded
- `502 Bad Gateway` — upstream service unavailable
