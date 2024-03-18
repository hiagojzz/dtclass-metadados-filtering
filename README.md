# Filtragem de dataclasses e metadas

Definição de filtragem de metadata.

```python
from typing import Any
from dataclasses import dataclass, asdict


@dataclass
class TemplateAttributes:
    ui_visible: bool = False
    api_visible: bool = False

    def dict(self) -> dict[str, Any]:
        return asdict(self)
```

```python
from dataclasses import dataclass, field

from template_attributes import TemplateAttributes

@dataclass
class ComputeTemplate:

    instance_type: str = field(
        metadata=TemplateAttributes(
            api_visible=True
        ).dict()
    )

    instance_count: int = field(
        metadata=TemplateAttributes(
            ui_visible=True,
            api_visible=True
        ).dict()
    )

    instance_secret: str = field(
        metadata=TemplateAttributes().dict()
    )
```

```python
from template_attributes import TemplateAttributeField
from template_utils import TemplateUtils

def api_visible_decorator(func):

    def wrapper(*args, **kwargs):

        processed_dataclasses = TemplateUtils.repr(
            dataclazzes=kwargs.get("dataclazzes"),
            repr_type=TemplateAttributeField.API_VISIBLE
        )

        kwargs["dataclazzes"] = processed_dataclasses

        func(*args, **kwargs)

    return wrapper
```

```bash
python3 runner.py 

Tests

#################################

Instantiate templates with template attributes

ComputeTemplate(instance_type='c24.xlarge', instance_count=6, instance_secret='Você não deveria estar me vendo hehehe!')

ApplicationTemplate(version='2023.6', precision='DOUBLE', secret='You should not see me!')

Apply representations

API Visible

[
    {
        "instance_type": "c24.xlarge",
        "instance_count": 6
    },
    {
        "version": "2023.6"
    }
]

UI Visible

[
    {
        "instance_count": 6
    },
    {
        "version": "2023.6",
        "precision": "DOUBLE"
    }
]
```
